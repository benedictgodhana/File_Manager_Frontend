<template>
  <v-app>
    <v-container  fluid>
      <h1 v-if="!inFolderView"></h1>

      <v-toolbar style="background-color: darkblue;" rounded>
          <v-card-title>File Management System</v-card-title>
          <v-spacer></v-spacer>
          <v-btn icon @click="deleteSelectedItems" title="Delete" elevation="0">
            <v-icon color="red">mdi-delete</v-icon>
          </v-btn>
          <v-btn icon @click="renameSelectedItem" title="Rename" elevation="0">
            <v-icon>mdi-rename-box</v-icon>
          </v-btn>
          <v-btn icon @click="copySelectedItems" title="Copy" elevation="0">
            <v-icon>mdi-content-copy</v-icon>
          </v-btn>
          <v-btn icon @click="pasteItems" title="Paste" elevation="0" :disabled="!clipboard.length">
            <v-icon>mdi-content-paste</v-icon>
          </v-btn>
          <v-btn color="grey darken-2" dark @click="navigateToUploadPage">Upload</v-btn>
          <v-btn @click="showAddRootDialog" color="primary" class="mb-2" style="text-transform: capitalize;" v-if="!inFolderView">Add a Directory</v-btn>

        </v-toolbar>

      <!-- Add Directory Button -->
      <br>
      <br>

      <!-- Search Field -->
      <v-text-field
        v-model="searchQuery"
        label="Search Directories"
        @input="filterDirectories"
        variant="outlined"
        v-if="!inFolderView"
      ></v-text-field>

      <!-- Directory View -->
      <div v-if="!inFolderView" class="directory-container">
        <v-row>
          <v-col
            v-for="directory in filteredRootDirectories"
            :key="directory.id"
            cols="12" md="3" lg="2"
          >
            <v-card class="directory-item" @click="selectDirectory(directory)" elevation="0">
              <v-card-title class="text-wrap" style="font-size: 12px;">
                <v-icon left color="yellow" size="74">mdi-folder</v-icon>
                {{ directory.name }}
              </v-card-title>
            </v-card>
          </v-col>
        </v-row>
      </div>

      <!-- Subdirectories View -->
      <div v-else>
        <v-toolbar color="primary">
          <v-card-title>Subdirectories of {{ selectedDirectory.name }}</v-card-title>
          <v-spacer></v-spacer>
          <v-btn @click="backToRoot" color="neutral" class="mb-2" style="text-transform: capitalize;">Close</v-btn>
        </v-toolbar>

        <br>
        <v-row>
          <v-col
            v-for="child in selectedDirectory.children"
            :key="child.id"
            cols="12" md="3" lg="2"
          >
            <v-card class="directory-item" @click="selectChildDirectory(child)" elevation="0">
              <v-card-title style="font-size: 12px;">
                <v-icon left color="yellow" size="74">mdi-folder</v-icon>
                {{ child.name }}
              </v-card-title>
            </v-card>
          </v-col>
        </v-row>

  <!-- Files View -->
<v-row class="mt-4">
  <v-col v-if="selectedChildDirectory && selectedChildDirectory.files && selectedChildDirectory.files.length > 0">
    <h3>Files in {{ selectedChildDirectory.name }}</h3>
    <br>
    <v-btn color="primary" @click="addFile">Add File</v-btn>
    <br><br>
    <v-data-table
      :headers="fileHeaders"
      :items="selectedChildDirectory.files"
      class="elevation-1"
      item-key="id"
    >
      <template v-slot:item.file_name="{ item }">
        <v-icon class="mr-2">
          {{ getFileIcon(item.name) }} <!-- Use a method to get the icon based on the file name -->
        </v-icon>
        {{ item.name }}
      </template>

      <!-- Add Action Buttons Column -->
      <template v-slot:item.actions="{ item }">
        <v-btn icon @click="viewFile(item)">
          <v-icon>mdi-eye</v-icon>
        </v-btn>
        <v-btn icon @click="editFile(item)">
          <v-icon>mdi-pencil</v-icon>
        </v-btn>
        <v-btn icon @click="deleteFile(item)">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>
  </v-col>
</v-row>


      </div>

      <!-- Dialogs remain the same -->
      <!-- Dialog for Adding Root Directory -->
<!-- Dialog for Adding Root Directory -->
<v-dialog v-model="addRootDialog" max-width="800px" persistent>
  <v-card color="white">
    <v-toolbar color="white">
      <v-card-title>
        <v-icon left>mdi-folder-plus</v-icon>
        Add Folder
      </v-card-title>
      <v-spacer></v-spacer>
      <v-btn @click="addRootDialog = false" icon>
        <v-icon>mdi-close</v-icon>
      </v-btn>
    </v-toolbar>

    <v-card-text>
      <v-text-field
        v-model="newRootDirectory"
        label="Directory Name"
        :error-messages="directoryErrors"
        required
        prepend-icon="mdi-folder"
      ></v-text-field>
    </v-card-text>

    <v-card-actions>
      <v-btn color="primary" @click="addRootDirectory">
        <v-icon left>mdi-plus</v-icon> <!-- Added icon for the Add button -->
        Add
      </v-btn>
      <v-btn @click="addRootDialog = false">
        <v-icon left>mdi-cancel</v-icon> <!-- Added icon for the Cancel button -->
        Cancel
      </v-btn>
    </v-card-actions>
  </v-card>
</v-dialog>



      <!-- Dialog for Adding Child Directory -->
      <v-dialog v-model="addChildDialog" max-width="500px">
        <v-card>
          <v-card-title>Add Child Directory</v-card-title>
          <v-card-text>
            <v-text-field v-model="newChildDirectory" label="Directory Name"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="primary" @click="addChildDirectory">Add</v-btn>
            <v-btn @click="addChildDialog = false">Cancel</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <!-- Dialog for Adding File -->
      <v-dialog v-model="addFileDialog" max-width="500px">
        <v-card>
          <v-card-title>Add File</v-card-title>
          <v-card-text>
            <v-text-field v-model="newFile" label="File Name"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="primary" @click="addFile">Add</v-btn>
            <v-btn @click="addFileDialog = false">Cancel</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>


      <v-snackbar v-model="snackbar" :color="snackbarColor" timeout="3000">
  {{ snackbarMessage }}
</v-snackbar>

    </v-container>
  </v-app>
</template>

<script>
import axiosInstance from '../plugins/axiosInstance';

export default {
  data() {
    return {
      rootDirectories: [],
      filteredRootDirectories: [],
      searchQuery: '',
      selectedDirectory: null,
      selectedChildDirectory: null, // Track the selected child directory
      inFolderView: false, // Track if in folder view
      addRootDialog: false,
      addChildDialog: false,
      addFileDialog: false,
      newRootDirectory: '',
      newChildDirectory: '',
      directoryErrors: [], // Array to hold error messages
      snackbar: false, // Control the snackbar visibility
    snackbarMessage: '', // Snackbar message
    snackbarColor: '', // Snackbar color (success or error)
      newFile: '',
      clipboard: [],
      currentParentDirectoryId: null, // Track the current parent directory ID for adding children
      currentDirectoryId: null, // Track the current directory ID for adding files
      fileHeaders: [
        {
          title: 'File',
          value: 'file_name', // This is the key for the custom slot
        },
        {
          title: 'Size',
          value: 'size', // Assuming you have a size property in your file objects
        },
        {
          title: 'File Image',
          value: 'path', // Assuming you have a lastModified property in your file objects
        },
        {
          title: 'Directory',
          value: 'directory_id', // Assuming you have a lastModified property in your file objects
        },

        {
          title: 'Actions',
          value: 'actions', // Column for action buttons
          sortable: false, // Disable sorting for action buttons
        },
      ],
    };
  },
  methods: {
    async fetchRootDirectories() {
      try {
        const response = await axiosInstance.get('/directories');
        this.rootDirectories = response.data;
        this.filteredRootDirectories = this.rootDirectories.filter(dir => !dir.parent_id); // Filter to get root directories only
        // Add children to their respective parents
        this.rootDirectories.forEach(directory => {
          if (directory.parent_id) {
            const parent = this.rootDirectories.find(parent => parent.id === directory.parent_id);
            if (parent) {
              parent.children = parent.children || []; // Initialize children array
              parent.children.push(directory); // Add directory as child
            }
          }
        });
      } catch (error) {
        console.error('Error fetching root directories:', error);
      }
    },
    filterDirectories() {
      const query = this.searchQuery.toLowerCase();
      this.filteredRootDirectories = this.rootDirectories.filter(directory =>
        directory.name.toLowerCase().includes(query) && !directory.parent_id // Ensure we are only filtering root directories
      );
    },
    selectDirectory(directory) {
      this.selectedDirectory = directory; // Show selected directory's subdirectories
      this.inFolderView = true; // Enter folder view
      this.selectedChildDirectory = null; // Clear any previously selected child directory
    },
    backToRoot() {
      this.selectedDirectory = null; // Clear selected directory
      this.selectedChildDirectory = null; // Clear selected child directory
      this.inFolderView = false; // Go back to root view
    },
    showAddRootDialog() {
      this.addRootDialog = true; // Show dialog to add root directory
    },
    async addRootDirectory() {
    // Clear previous errors
    this.directoryErrors = [];

    // Validation: Check if the directory name is empty
    if (!this.newRootDirectory) {
      this.directoryErrors.push('Directory name is required.');
      return;
    }

    // Validation: Check if the directory name is too short or too long
    if (this.newRootDirectory.length < 3) {
      this.directoryErrors.push('Directory name must be at least 3 characters long.');
      return;
    }

    if (this.newRootDirectory.length > 50) {
      this.directoryErrors.push('Directory name must not exceed 50 characters.');
      return;
    }

    // Clear errors if validation passes
    this.directoryErrors = [];

    try {
      const response = await axiosInstance.post('/directories', { name: this.newRootDirectory });
      this.rootDirectories.push(response.data);
      this.filteredRootDirectories.push(response.data); // Add to filtered root directories
      this.newRootDirectory = ''; // Clear the input field
      this.addRootDialog = false; // Close the dialog

      // Display success snackbar
      this.showSnackbar('Root directory added successfully!', 'success');
    } catch (error) {
      console.error('Error adding root directory:', error);
      this.showSnackbar('Failed to add directory.', 'error');
    }
  },

  showSnackbar(message, color) {
    this.snackbarMessage = message;
    this.snackbarColor = color;
    this.snackbar = true; // Show snackbar
  },


  getFileIcon(fileName) {
      // Logic to return the appropriate icon based on the file type
      const extension = fileName.split('.').pop().toLowerCase();
      switch (extension) {
        case 'pdf':
          return 'mdi-file-pdf'; // Example for PDF files
        case 'doc':
        case 'docx':
          return 'mdi-file-word'; // Example for Word documents
        case 'xls':
        case 'xlsx':
          return 'mdi-file-excel'; // Example for Excel files
        case 'ppt':
        case 'pptx':
          return 'mdi-file-powerpoint'; // Example for PowerPoint files
        default:
          return 'mdi-file'; // Default icon for unknown file types
      }
    },

    showAddChildDialog(directoryId) {
      this.addChildDialog = true; // Show dialog to add child directory
      this.currentParentDirectoryId = directoryId; // Store the ID of the parent directory
    },
    async addChildDirectory() {
      try {
        const response = await axiosInstance.post(`/directories/${this.currentParentDirectoryId}/children`, { name: this.newChildDirectory });
        const parentDirectory = this.rootDirectories.find(dir => dir.id === this.currentParentDirectoryId);
        if (parentDirectory) {
          parentDirectory.children = parentDirectory.children || []; // Initialize children array
          parentDirectory.children.push(response.data);
        }
        this.addChildDialog = false;
        this.newChildDirectory = '';
      } catch (error) {
        console.error('Error adding child directory:', error);
      }
    },


    navigateToUploadPage() {
      this.$router.push('/upload');
    },
    selectChildDirectory(child) {
      // Handle selecting a child directory
      this.selectedChildDirectory = child; // Set the selected child directory
      console.log('Selected child directory:', child);
      // Fetch files for the selected child directory if necessary
      // You can replace this with a call to fetch files from the server if needed
      this.fetchFilesForDirectory(child.id);
    },
    async fetchFilesForDirectory(directoryId) {
      try {
        const response = await axiosInstance.get(`/directories/${directoryId}/files`);
        this.selectedChildDirectory.files = response.data; // Assuming your API returns files in this format
      } catch (error) {
        console.error('Error fetching files:', error);
      }
    },
    showAddFileDialog(child) {
      this.addFileDialog = true; // Show dialog to add file
      this.currentDirectoryId = child.id; // Store the ID of the selected child directory
    },
    async addFile() {
      try {
        const response = await axiosInstance.post(`/directories/${this.currentDirectoryId}/files`, { name: this.newFile });
        if (this.selectedChildDirectory.files) {
          this.selectedChildDirectory.files.push(response.data); // Add file to the selected child's files
        } else {
          this.selectedChildDirectory.files = [response.data]; // Initialize if it was empty
        }
        this.addFileDialog = false;
        this.newFile = '';
      } catch (error) {
        console.error('Error adding file:', error);
      }
    },
  },
  mounted() {
    this.fetchRootDirectories(); // Fetch root directories on mount
  },
};
</script>

<style scoped>
.directory-container {
  display: flex;
  flex-wrap: wrap;
}
.directory-item {
  cursor: pointer;
}
</style>
