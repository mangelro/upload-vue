# upload-vue

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

###################################################################################################
###################################################################################################

## https://serversideup.net/courses/guide-uploading-files-vuejs-axios/

Para recuperar los fichero subidos en .NET 5.0

Upload un solo fichero (limitando su tamaño)

        [RequestSizeLimit(bytes: 60_000_000)]
        public IActionResult Post(IFormFile file){
            ...
        }

Upload varios ficheros (IEnumerable, ICollection tambien válido)
        public IActionResult Post(IList<IFormFile> files){
            ...
        }


Upload fichero(s) y campos adicionales
        public class Document
        {
            public string Title { get; set; }
            public string Version { get; set; }
            IFormFile Files { get; set; }
        }

        public IActionResult Post([FromForm] Document document){

        }

MUY IMPORTANTE!!!! El nombre del parametro que recibe los ficheros (file/files) debe coresponder con el nombre del input file