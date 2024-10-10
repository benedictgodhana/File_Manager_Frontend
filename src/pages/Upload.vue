<template>
  <v-container fluid>
    <v-toolbar color="white">
      <v-btn @click="$router.push('/')" icon>
        <v-icon>mdi-arrow-left</v-icon>
      </v-btn>
      <v-toolbar-title>Upload Files</v-toolbar-title>
    </v-toolbar>

    <v-card class="mt-5" color="white">
      <v-card-title>
        <span class="headline">Upload File/Folder</span>
      </v-card-title>
      <v-card-text>
        <div
          @drop.prevent="handleDrop"
          @dragover.prevent
          @click="openFileDialog"
          class="drop-area"
        >
          <v-file-input
            ref="fileInput"
            v-model="selectedFiles"
            label="Choose a file or folder"
            prepend-icon="mdi-paperclip"
            accept=".pdf,.doc,.docx,.ppt,.pptx,.txt,.zip"
            webkitdirectory
            multiple
            style="display: none;"
            @change="handleFileUpload"
          ></v-file-input>
          <p v-if="!selectedFiles">Drag & drop files here or click to upload</p>
          <p v-if="selectedFiles">Selected Files: {{ selectedFiles.length }}</p>
        </div>

        <v-progress-linear
          v-if="uploading"
          :value="uploadProgress"
          height="20"
          color="green"
        ></v-progress-linear>

        <p v-if="uploading" class="mt-2">{{ uploadProgress }}% Uploaded</p>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn
          color="green darken-1"
          text
          @click="uploadFiles"
          :disabled="!selectedFiles || uploading"
        >
          Upload
        </v-btn>
        <v-btn text @click="cancelUpload" :disabled="!uploading">Cancel</v-btn>
      </v-card-actions>
    </v-card>

    <!-- Snackbar for successful upload -->
    <v-snackbar
      v-model="snackbar"
      color="green"
      timeout="3000"
      multi-line
    >
      Your files have been uploaded successfully!
      <v-btn text @click="snackbar = false">Close</v-btn>
    </v-snackbar>

    <!-- Alert Dialog -->
    <v-dialog v-model="showAlert" max-width="290">
      <v-card>
        <v-card-title class="headline">Upload Complete</v-card-title>
        <v-card-text>
          Your files have been uploaded successfully!
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" @click="closeAlert">OK</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from '../plugins/axiosInstance';

export default {
  data() {
    return {
      selectedFiles: null,
      uploading: false,
      uploadProgress: 0,
      showAlert: false,
      snackbar: false,
    };
  },
  methods: {
    openFileDialog() {
      this.$refs.fileInput.click();
    },
    handleFileUpload(event) {
      this.selectedFiles = event.target.files;
    },
    handleDrop(event) {
      const files = event.dataTransfer.files;
      if (files.length > 0) {
        this.selectedFiles = files;
      }
    },
    async uploadFiles() {
      if (!this.selectedFiles) return;

      this.uploading = true;
      this.uploadProgress = 0;

      // Prepare FormData
      const formData = new FormData();
      for (let i = 0; i < this.selectedFiles.length; i++) {
        formData.append('files[]', this.selectedFiles[i]);
      }

      try {
        const response = await axios.post('/files/upload', formData, {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
          onUploadProgress: (progressEvent) => {
            // Update progress bar during the upload
            this.uploadProgress = Math.round(
              (progressEvent.loaded * 100) / progressEvent.total
            );
          },
        });

        // On success, reset states and show success notification
        this.snackbar = true;
        this.selectedFiles = null;
        this.uploadProgress = 0;
        this.uploading = false;

        console.log('Files uploaded successfully:', response.data);
      } catch (error) {
        console.error('File upload failed:', error);
        this.uploading = false;
      }
    },
    cancelUpload() {
      this.uploading = false;
      this.uploadProgress = 0;
      this.selectedFiles = null;
    },
    getFileType(fileName) {
      const extension = fileName.split('.').pop().toLowerCase();
      return ['pdf', 'doc', 'docx', 'ppt', 'pptx', 'txt', 'zip'].includes(extension)
        ? 'Document'
        : 'File';
    },
  },
};

</script>

<style scoped>
.drop-area {
  border: 2px dashed #ccc;
  border-radius: 4px;
  padding: 20px;
  text-align: center;
  cursor: pointer;
  margin-bottom: 20px;
}
.drop-area:hover {
  background-color: #f9f9f9;
}
.v-progress-linear {
  margin-top: 10px;
}
</style>
