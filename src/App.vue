<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
<div class="container">
  <div class="row">
    <div class="col s12">
      <drop-zone url="http://localhost:41321/files" :maxFiles="3"  
        @uploading-completed="onFileUploadingCompleted">
        <template #input={files}>
          <div>
            <input type="text" name="title">
            <input type="text" name="version"> 
            <!--input hidden con el message digest-->
            <input type="hidden" name="md" v-for="(f,i) in files" :key="i"  :value="f.md">
          </div>
        </template>

        <template #default={ProcessQueue,ClearQueue}>
          <div class="upload-button-container">
            <nav class="center-align">
              <button @click="ProcessQueue" type="submit"><i>upload</i>Subir los ficheros</button>
              <button @click="ClearQueue"><i>clear</i>Cancelar subida</button>
            </nav>
          </div>
        </template>
      </drop-zone>
    </div>
  </div>
</div>
</template>

<script>
import DropZone from './components/DropZone'

export default {
  name: 'App',
  
  components: {
    DropZone
  },

  methods:{
  
    onFileUploadingCompleted({name,status}){
      console.log('event onFileUploadingCompleted',name,status)
    },
  }
}
</script>

<style>
::root {
  font-family: Roboto, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.upload-button-container{
  padding: 10px;
}
</style>
