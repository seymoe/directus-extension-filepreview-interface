<template>
	<div class="file-preview">
		<video v-if="['video'].includes(previewFileType)" controls :src="src" alt="" role="presentation" />
    <iframe v-if="['doc', 'ppt'].includes(previewFileType)" :src="`http://view.officeapps.live.com/op/view.aspx?src=${src}`" frameborder="0"></iframe>
    <iframe v-if="['pdf'].includes(previewFileType)" :src="src" frameborder="0"></iframe>
	</div>
</template>

<script>
import { defineComponent, ref, watch, computed, inject } from 'vue';
import { useApi } from '@directus/extensions-sdk'

function getRootPath() {
	const path = window.location.pathname
	const parts = path.split('/')
	const adminIndex = parts.indexOf('admin')
	const rootPath = parts.slice(0, adminIndex).join('/') + '/'
	return rootPath;
}
function addQueryToPath(path, query) {
	const queryParams = new URLSearchParams(path.split('?')[1] || '');
	for (const [key, value] of Object.entries(query)) {
		queryParams.set(key, value);
	}
	return path.split('?')[0] + '?' + queryParams;
}

function addTokenToURL(url, token) {
	if (!token) return url;
	return addQueryToPath(url, { access_token: token });
}

export default defineComponent({
	props: {
		value: {
			type: [String, Object],
			default: null,
		},
		fileField: {
      type: String,
      default: ''
    },
    assetsToken: {
      type: String,
      default: ''
    }
	},
	emits: ['input'],
	setup(props, { emit }) {
    const api = useApi()
    console.log(api)
		const file = ref(null);
    const values = inject('values')

		const loading = ref(false);

		const src = computed(() => {
			if (!file.value) return null;

			if (previewFileType) {
				return addTokenToURL( previewFileType === 'video' ? '' : window.location.origin +  getRootPath() + `assets/${file.value.id}`, props.assetsToken);
			}
			return null;
		});

    const previewFileType = computed(() => {
      if (!file.value) return null
      let fileName = file.value.filename_download
      let len = fileName.length
      if (fileName.indexOf('.mp4') === len - 4) {
        return 'video'
      }
      if (fileName.indexOf('.doc') === len - 4 || fileName.indexOf('.docx') === len - 5) {
        return 'doc'
      }
      if (fileName.indexOf('.ppt') === len - 4 || fileName.indexOf('.pptx') === len - 5) {
        return 'ppt'
      }
      if (fileName.indexOf('.pdf') === len - 4) {
        return 'pdf'
      }
      return null
    })

		watch(
			() => values.value,
			(newValue) => {
				if (newValue) {
					fetchImage();
				}
			},
			{ immediate: true }
		);

		return {
			file,
			loading,
      previewFileType,
			src
		};

		async function fetchImage() {
			loading.value = true;
			try {
        let id = (props.fileField && values.value[props.fileField]) ? values.value[props.fileField] : ''
        if (!id) return
				const response = await api.get(`/files/${id}`, {
					params: {
						fields: ['id', 'title', 'width', 'height', 'filesize', 'type', 'filename_download'],
					},
				});
        file.value = response.data.data
			} catch (err) {
				console.log(err)
			} finally {
				loading.value = false;
			}
		}
	}
});
</script>

<style scoped>
.file-preview video {
  display: block;
  margin: 0 auto;
  z-index: 1;
  width: 100%;
}
.file-preview iframe{
  width: 100%;
  min-height: 500px;
}
</style>
