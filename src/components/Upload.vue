<template>
  <v-container>
    <v-toolbar color="white">
      <v-btn @click="$router.push('/files')" icon>
        <v-icon>mdi-arrow-left</v-icon>
      </v-btn>
      <v-toolbar-title>Upload Files</v-toolbar-title>
    </v-toolbar>

    <v-card class="mt-5">
      <v-card-title>
        <span class="headline">Upload File/Folder</span>
      </v-card-title>
      <v-card-text>
        <v-file-input
          v-model="selectedFiles"
          label="Choose a file or folder"
          prepend-icon="mdi-paperclip"
          accept=".pdf,.doc,.docx,.ppt,.pptx,.txt,.zip"
          webkitdirectory
          multiple
          @change="handleFileUpload"
        ></v-file-input>

        <v-progress-linear
          v-if="uploading"
          :value="uploadProgress"
          height="20"
          color="green"
        ></v-progress-linear>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="green darken-1" text @click="uploadFiles" :disabled="!selectedFiles || uploading">Upload</v-btn>
        <v-btn text @click="cancelUpload" :disabled="!uploading">Cancel</v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      selectedFiles: null,
      uploading: false,
      uploadProgress: 0,
    };
  },
  methods: {
    handleFileUpload(event) {
      this.selectedFiles = event.target.files;
    },
    async uploadFiles() {
      this.uploading = true;
      this.uploadProgress = 0;

      // Simulate upload progress for demonstration
      for (let i = 0; i <= 100; i += 10) {
        await new Promise((resolve) => setTimeout(resolve, 200));
        this.uploadProgress = i;
      }

      // Handle file upload logic here
      if (this.selectedFiles) {
        for (let i = 0; i < this.selectedFiles.length; i++) {
          // Create a new file object and add it to the files array in the previous component
          const newFile = {
            name: this.selectedFiles[i].name,
            type: this.getFileType(this.selectedFiles[i].name),
            last_modified: new Date().toISOString().split('T')[0],
            size: this.selectedFiles[i].size,
          };
          this.$emit('file-uploaded', newFile);
        }
      }

      this.uploading = false;
      this.uploadProgress = 0;
      this.$router.push('/files'); // Redirect back to files page
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
.v-progress-linear {
  margin-top: 10px;
}
</style>
