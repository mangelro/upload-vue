<template>
   <form
    :action="url"
    class="dropzone needsclick dz-clickable"
    id="dropzone"
    enctype="multipart/form-data">
    <!-- this is were the previews should be shown. -->
    <div class="previews dropzone-previews"></div>
    <!-- Now setup your input fields -->
    <slot name="input" :files="filesToProcess"></slot>
  </form> 
  <slot :ProcessQueue="processQueue" :ClearQueue="clearQueue" >
    <button @click="processQueue">Subir cola</button>
  </slot>
</template>


<script>
import CryptoJS from 'crypto-js'
//import md5 from 'crypto-js/md5'
//import Base64 from 'crypto-js/enc-base64'

import {Dropzone} from "dropzone"
import msg from "./DropZoneMsg"

export default {
  props: {
    url: {
      type: String,
      required: true,
    },
    maxFiles: {
      type: Number,
      required: false,
      default: 1,
    },
    acceptedFiles: {
      type: String,
      requred: false,
    },
  },

  data() {
    return {
      myDropzone: null,
      filesToProcess:[]
    }
  },

  mounted() {
    Dropzone.autoDiscover = false
    this.myDropzone = new Dropzone("#dropzone", {

      paramName: this._trickParamName, // The name that will be used to transfer the file
      previewsContainer:'.previews', // Defines where to display the file previews
      maxFilesize: 100, // MB

      uploadMultiple:this.maxFiles > 1,
      maxFiles: this.maxFiles,

      parallelUploads: 20,

      addRemoveLinks:true,
      
      //chunking:true,
      // acceptedFiles: "image/*",

      acceptedFiles: this.acceptedFiles,
      autoProcessQueue: false, //se necesita llamar explicitamente a .processQueue() para subir los ficheros.)

      //headers:{'x-api-version':'1.0'},

      //Mensajes importados
      ...msg, 

    })

    this.myDropzone.on("addedfile", this.onFileAdded)
    this.myDropzone.on("removedfile", this.onRemovedFile)
    this.myDropzone.on("complete", this.onCompleted)
  },

  emits:['uploadingCompleted','fileAdded','preProcessQueue'],

  methods: {
    /**
     *  Pequeño truco para evitar que le añada [n] a los parámetros con el
     *  nombre de los ficheros en subidas multiples
     */
    _trickParamName() {
      return this.maxFiles > 1? "files":"file"
    },
    /**
     * 
     * 
     * 
     */
    async _calculoDigest(file){
      const buffer = await file.arrayBuffer()
      const digest=CryptoJS.MD5(CryptoJS.lib.WordArray.create(buffer))
      return  CryptoJS.enc.Base64.stringify (digest)
    },

    async onFileAdded(file) {
      // file.previewElement.addEventListener("click", () => {
      //   this.myDropzone.removeFile(file)
      // })

      // Cálculo de message diggest
      file.md=await this._calculoDigest(file)
      
      console.log("File added", file.md)
      this.$emit('fileAdded',file)
    },

    onRemovedFile(file) {
      console.log("File removed", file.name)
    },

    /**
     * Evento que se proudce por cada uno de los ficheros procesados
     */
    onCompleted(file){
      console.log("File Uploading Completed",file)
      this.$emit('uploadingCompleted',file)
      setTimeout(() => this.myDropzone.removeFile(file),5000)
    },

    /**
     * Procesa la cola de ficheros
     */
    processQueue() {
      console.log("Pre-Process Queue", this.myDropzone)
      this.filesToProcess=this.myDropzone.files
      this.$emit('preProcessQueue',this.myDropzone.files)
      console.log("Process Queue", this.myDropzone)

      this.$nextTick(()=>this.myDropzone.processQueue())
      
    },

    /**
     * Limpia la cola de ficheros
     */
    clearQueue(){
      console.log("Clear Queue", this.myDropzone.files)
      this.myDropzone.files.forEach(f=>this.myDropzone.removeFile(f))
    }

  },
}
</script>
