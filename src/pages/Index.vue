<template>
  <q-page class="flex flex-center">
    <button @click="uploadFile">UPLOAD</button>
    <button @click="downloadFile" >DOWNLOAD</button>
    <h3>{{STATUS_FILE}}</h3>
  </q-page>
</template>

<style>
</style>

<script>
/* global FileTransfer, FileUploadOptions */

let fileURL = cordova.file.externalApplicationStorageDirectory + 'files/thumbado.png'

export default {
  name: 'PageIndex',
  data () {
    return {
      pathToFile: '',
      STATUS_FILE: 'LOADING'
    }
  },
  created () {
    this.STATUS_FILE = 'Check if Exists'
    window.resolveLocalFileSystemURL(fileURL, this.loadingFile, this.downloadFile)
  },
  methods: {
    loadingFile: function () {
      this.STATUS_FILE = 'FILE EXISTS!'
      console.log('FILE EXISTS!')
    },

    downloadFile: function (event) {
      let vm = this
      console.log('DOWNLOAD FILE....')
      this.STATUS_FILE = 'DOWNLOAD FILE....'

      var fileTransfer = new FileTransfer()
      var uri = encodeURI('http://census.inf.br/app/magazines/upload/2/thumb.png')

      fileTransfer.download(
        uri, fileURL, function (entry) {
          vm.STATUS_FILE = 'DOWNLOAD FINISH, NOW YOU CAN SEE FILE IN:' + entry.toURL()
          console.log('download complete: ' + entry.toURL())
        },
        function (error) {
          console.log('download error source ' + error.source)
          console.log('download error target ' + error.target)
          console.log('download error code' + error.code)
        }

        // ,false, {headers: {'Authorization': 'Basic dGVzdHVzZXJuYW1lOnRlc3RwYXNzd29yZA=='}}
      )
    },
    uploadFile: function (event) {
      console.log(fileURL)
      var uri = encodeURI('https://inwork.com.br/upload_file/upload_files.php')
      var options = new FileUploadOptions()
      options.fileKey = 'arquivo'
      options.fileName = fileURL.substr(fileURL.lastIndexOf('/') + 1)
      options.mimeType = 'text/plain'

      var headers = { 'headerParam': 'headerValue' }
      options.headers = headers
      var ft = new FileTransfer()
      ft.upload(fileURL, uri, onSuccess, onError, options)

      function onSuccess (r) {
        console.log('Code = ' + r.responseCode)
        console.log('Response = ' + r.response)
        console.log('Sent = ' + r.bytesSent)
      }

      function onError (error) {
        alert('An error has occurred: Code = ' + error.code)
        console.log('upload error source ' + error.source)
        console.log('upload error target ' + error.target)
      }
    }
  }
}
</script>
