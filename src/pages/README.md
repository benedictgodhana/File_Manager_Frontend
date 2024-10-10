# File Management System Frontend

<p align="center">
  <a href="https://vuejs.org" target="_blank">
    <img src="https://vuejs.org/images/logo.png" width="200" alt="Vue.js Logo">
  </a>
</p>

<p align="center">
  <a href="https://github.com/vuejs/vue">
    <img src="https://img.shields.io/github/stars/vuejs/vue" alt="Stars">
  </a>
  <a href="https://npmjs.com/package/vue">
    <img src="https://img.shields.io/npm/v/vue" alt="Version">
  </a>
  <a href="https://opensource.org/licenses/MIT">
    <img src="https://img.shields.io/badge/license-MIT-green" alt="License">
  </a>
</p>

## About the File Management System Frontend

The File Management System frontend is a Vue.js application designed to provide a user-friendly interface for managing files and directories through the RESTful APIs created by the backend. It enables users to upload, organize, and access files efficiently.

### Key Features

- **Dynamic Routing**: Vue components in this project automatically become navigable routes.
- **File Management**: Upload, download, and manage files directly from the interface.
- **Directory Organization**: Create, rename, and delete directories to keep files organized.
- **Search Functionality**: Quickly find files using search capabilities.
- **Responsive Design**: The application is designed to work on various devices.

## Pages

Vue components created in the `src/pages` folder will automatically be converted to navigable routes. You can create new components in this folder, and they will be registered as routes without additional configuration.

### Dynamic Routing with `unplugin-vue-router`

Full documentation for this feature can be found in the official [unplugin-vue-router](https://github.com/posva/unplugin-vue-router) repository.

### Example Structure

Here's how your `src/pages` folder might look:

src/ └── pages/ ├── Home.vue ├── Upload.vue ├── FileList.vue └── NotFound.vue


### Creating a New Page

To create a new page:

1. Add a new `.vue` component file in the `src/pages` directory.
2. Name the file according to the route you want (e.g., `Upload.vue` for `/upload`).
3. Implement your component logic and template.

## How to Run the Frontend

### Prerequisites

- Node.js >= 21.0.6
- npm or yarn

### Installation Steps

1. **Clone the Repository**
   ```bash
   git clone https://github.com/benedictgodhana/file-management-system_frontend.git
   cd file-management-system_frontend
