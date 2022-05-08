# File Preview Interface - Directus extension

A file freview interface.

## Installation

In your Directus installation root

```
npm install directus-extension-filepreview-interface
```

Restart directus

## Usage


## Building locally and contributing

You can also clone this repository and build it by yourself.

```
npm i
npm run build
```

Then use `index.js` in your custom `/extensions/panels` directory or in whatever you want.


.env 增加配置

CONTENT_SECURITY_POLICY_directives__child-src="'self' blob: https://view.officeapps.live.com"
