<template>
  <v-app>
    <!-- Sidebar Navigation Menu -->
    <v-navigation-drawer app permanent dark color="black">
      <!-- Logo at the top -->
      <v-img
        src="/logo.png"
        max-height="300"
        max-width="200"
        class="mx-auto my-10"
      ></v-img>

      <v-list dense>
        <v-list-item prepend-icon="mdi-home" title="Home" link :to="'/'"></v-list-item>
        <v-list-item prepend-icon="mdi-folder" title="Files" link :to="'/files'"></v-list-item>
        <v-list-item prepend-icon="mdi-image" title="Photos" link :to="'/photos'"></v-list-item>
        <v-list-item prepend-icon="mdi-account-multiple" title="Shared with you" link :to="'/shared'"></v-list-item>
      </v-list>

      <v-divider></v-divider>
      <v-list dense>
        <v-list-item>
          <v-btn block color="grey darken-2" class="mb-3" dark rounded style="text-transform: capitalize;" @click="openNewFolderDialog">
            <v-icon left>mdi-folder-plus</v-icon> New folder
          </v-btn>
        </v-list-item>

        <!-- List Group for Folders -->
        <v-list-group v-if="folders.length" sub-group>
          <template v-slot:activator>
            <v-list-item-title>Folders</v-list-item-title>
          </template>
          <v-list-item-group>
            <v-list-item v-for="folder in folders" :key="folder.id">
              <v-list-item-content>
                <v-list-item-title>{{ folder.name }}</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list-item-group>
        </v-list-group>

        <v-list-item prepend-icon="mdi-laptop" title="My drive"></v-list-item>
        <v-list-item prepend-icon="mdi-monitor" title="Computer"></v-list-item>
      </v-list>
    </v-navigation-drawer>

    <!-- Main Content Area -->
    <v-dialog v-model="newFolderDialog" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Create New Folder</span>
        </v-card-title>
        <v-card-text>
          <v-radio-group v-model="folderAction" column>
            <v-radio label="Create a folder" value="create" />
            <v-radio label="Upload a folder" value="upload" />
          </v-radio-group>

          <v-text-field
            v-if="folderAction === 'create'"
            v-model="folderName"
            label="Folder Name"
            outlined
          ></v-text-field>

          <v-file-input
            v-if="folderAction === 'upload'"
            v-model="folderUpload"
            label="Select a folder"
            outlined
            multiple
            directory
          ></v-file-input>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn @click="createOrUploadFolder">Submit</v-btn>
          <v-btn @click="newFolderDialog = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      newFolderDialog: false,
      folderAction: 'create', // Default action
      folderName: '', // For creating a new folder
      folderUpload: null, // For uploading a folder
      folders: [], // Array to hold folders
    };
  },
  methods: {
    openNewFolderDialog() {
      this.newFolderDialog = true;
      this.folderAction = 'create'; // Reset to default
      this.folderName = ''; // Clear the folder name
      this.folderUpload = null; // Clear previous uploads
    },
    createOrUploadFolder() {
      if (this.folderAction === 'create') {
        const newFolder = { id: Date.now(), name: this.folderName }; // Create a new folder object
        this.folders.push(newFolder); // Add it to the folders array
        console.log(`Creating folder: ${this.folderName}`);
      } else if (this.folderAction === 'upload') {
        // Logic to upload a folder can go here
        console.log(`Uploading folder: ${this.folderUpload}`);
        // Assuming folderUpload has a name property
        const uploadFolder = { id: Date.now(), name: this.folderUpload.name }; // Create an upload folder object
        this.folders.push(uploadFolder); // Add it to the folders array
      }
      this.newFolderDialog = false; // Close the dialog
    },
  },
};
</script>

<style scoped>
.v-navigation-drawer {
  width: 300px;
}

.v-list-item {
  margin-bottom: 12px;
}

.v-btn {
  justify-content: flex-start;
}

/* Optional styling to center the logo */
.v-img {
  display: block;
  margin: 0 auto;
}
</style>
