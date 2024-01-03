<template>
 <div class="csv-uploader-container" :class="{ 'dark-mode': isDarkMode }">
    <div class="csv-uploader">
      <label class="file-input-label">
        <input type="file" ref="fileInput" @change="handleFileChange" class="file-input" accept=".csv" />
        <span class="browse-icon">📂 Browse</span>
      </label>
      
      <button @click="uploadFile" class="upload-btn">Upload</button>
      <button @click="downloadFile" class="download-btn">Download</button>
      
      <button @click="toggleMode" class="mode-toggle-btn">
        {{ isDarkMode ? 'Light Mode' : 'Dark Mode' }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputFile: null,
      outputBlob: null,
      outputFileName: 'output.csv',
      isDarkMode: false,
    };
  },
  methods: {
    handleFileChange(event) {
      this.inputFile = event.target.files[0];
      this.outputBlob = null;
    },
    uploadFile() {
      if (!this.inputFile) {
        alert('Please choose a file to upload.');
        return;
      }

      if (!this.inputFile.name.toLowerCase().endsWith('.csv')) {
        alert('Please upload a CSV file.');
        return;
      }

      let formData = new FormData();
      formData.append('file', this.inputFile);

      fetch('http://localhost:8080/api/csv/upload', {
        method: 'POST',
        body: formData,
      })
      .then(response => response.blob())
      .then(blob => {
        this.outputBlob = blob;
      })
      .catch(error => console.error('Error uploading file:', error));
    },
    downloadFile() {
      if (this.outputBlob) {
        const downloadLink = document.createElement('a');
        const url = URL.createObjectURL(this.outputBlob);
        downloadLink.href = url;
        downloadLink.download = this.outputFileName;

        document.body.appendChild(downloadLink);
        downloadLink.click();
        document.body.removeChild(downloadLink);
      } else {
        alert('No output file available for download.');
      }
    },
    toggleMode() {
      this.isDarkMode = !this.isDarkMode;
    },
  },
};
</script>


<style src="./src/style/FileUpload.css" ></style>