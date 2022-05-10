# File Preview Interface - Directus extension

A file freview interface.

## Installation

In your Directus installation root

```
npm install directus-extension-filepreview-interface
```

Restart directus

## Usage

Create a field with File Preview, set field name that you want to preview, and set a static token. And then you should add config in `.env` file:

```
CONTENT_SECURITY_POLICY_directives__child-src="'self' blob: https://view.officeapps.live.com"
```

## Building locally and contributing

You can also clone this repository and build it by yourself.

```
npm i
npm run build
```

Then use `index.js` in your custom `/extensions/interfaces` directory or in whatever you want.

