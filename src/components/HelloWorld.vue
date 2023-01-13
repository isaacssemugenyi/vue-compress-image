<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="my-8">
      <UploadImages @changed="handleImages" />
      <button @click="logCompressedImages">LOG</button>
      <!-- <image-uploader
        :preview="true"
        :className="['fileinput', { 'fileinput--loaded': hasImage }]"
        capture="environment"
        :debug="1"
        :autoRotate="true"
        outputFormat="verbose"
        accept="image/*"
        doNotResize="['gif','svg']"
        @input="setImage"
      >
        <label for="fileInput" slot="upload-label">
          <figure>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="32"
              height="32"
              viewBox="0 0 32 32"
            >
              <path
                class="path1"
                d="M9.5 19c0 3.59 2.91 6.5 6.5 6.5s6.5-2.91 6.5-6.5-2.91-6.5-6.5-6.5-6.5 2.91-6.5 6.5zM30 8h-7c-0.5-2-1-4-3-4h-8c-2 0-2.5 2-3 4h-7c-1.1 0-2 0.9-2 2v18c0 1.1 0.9 2 2 2h28c1.1 0 2-0.9 2-2v-18c0-1.1-0.9-2-2-2zM16 27.875c-4.902 0-8.875-3.973-8.875-8.875s3.973-8.875 8.875-8.875c4.902 0 8.875 3.973 8.875 8.875s-3.973 8.875-8.875 8.875zM30 14h-4v-2h4v2z"
              ></path>
            </svg>
          </figure>
          <span class="upload-caption">{{
            hasImage ? "Replace" : "Click to upload"
          }}</span>
        </label>
      </image-uploader> -->
    </div>
  </div>
</template>

<script>
import UploadImages from "vue-upload-drop-images";
// import convert from "image-file-resize";
// import { blobToURL, fromBlob } from "image-resize-compress";
// import imageCompression from "browser-image-compression";

export default {
  name: "HelloWorld",
  components: {
    UploadImages,
  },
  data() {
    return {
      msg: "Vue Image Upload and Resize Demo",
      compressedImages: [],
      // hasImage: false,
      // image: null,
    };
  },
  methods: {
    logCompressedImages() {
      console.log(this.compressedImages);
    },
    handleImages(files) {
      console.log("original", files[0]);
      const MAX_WIDTH = 1920;
      const MAX_HEIGHT = 1080;
      // const MIME_TYPE = "image/jpeg";
      const QUALITY = 1;

      const blobURL = window.URL.createObjectURL(files[0]);
      const img = new Image();
      img.src = blobURL;
      img.onload = () => {
        window.URL.revokeObjectURL(blobURL); // release memory // Use the img
        const canvas = document.createElement("canvas");
        canvas.width = MAX_WIDTH;
        canvas.height = MAX_HEIGHT;

        canvas.toBlob(
          (blob) => {
            blob.lastModified = files[0]?.lastModified;
            blob.name = files[0]?.name;

            // const newImageDetails = {
            //   lastModified: files[0]?.lastModified,
            //   name: files[0]?.name,
            // };
            // blob = { size: blob?.size, type: blob?.type, ...newImageDetails };
            // Handle the compressed image
            this.compressedImages.push(blob);
          },
          files[0]?.type, //mimetype
          QUALITY
        );
      };
    },
    // setImage: function (output) {
    //   this.hasImage = true;
    //   this.image = output;
    //   console.log("Info", output.info);
    //   // console.log("Exif", output.exif);
    // },
    // async handleImages(files) {
    //   console.log("handleImages", files);
    //   // this.setImage(files[0]);

    //   // using image-file-resize
    //   // convert({
    //   //   file: files[0],
    //   //   width: 600,
    //   //   height: 400,
    //   //   type: "jpeg",
    //   // })
    //   //   .then((resp) => {
    //   //     // Response contain compressed and resized file
    //   //     console.log("compressed", resp);
    //   //   })
    //   //   .catch((error) => {
    //   //     // Error
    //   //     console.log(error);
    //   //   });
    //   // image-file-resize

    //   // image resize compress

    //   // quality value for webp and jpeg formats.
    //   // console.log("size", files[0]?.name);
    //   // const quality = 60;
    //   // // output width. 0 will keep its original width and 'auto' will calculate its scale from height.
    //   // const width = "auto";
    //   // // output height. 0 will keep its original height and 'auto' will calculate its scale from width.
    //   // const height = "auto";
    //   // // file format: png, jpeg, bmp, gif, webp. If null, original format will be used.
    //   // const format = null;

    //   // note only the blobFile argument is required
    //   // fromBlob(files[0], quality, width, height, format).then((blob) => {
    //   //   // will output the converted blob file
    //   //   const result = {
    //   //     name: files[0]?.name,
    //   //     lastModified: files[0]?.lastModified,
    //   //   };
    //   //   const blobUrl = blob;
    //   //   blob = { size: blob?.size, type: blob?.type, ...result };

    //   //   console.log("image", blob);
    //   //   // will generate a url to the converted file
    //   //   blobToURL(blobUrl).then((url) => console.log("url", url));
    //   // });
    //   // end image resize compress

    //   // browser-image-compression
    //   const imageFile = files[0];
    //   console.log("originalFile instanceof Blob", imageFile instanceof Blob); // true
    //   console.log(`originalFile size ${imageFile.size / 1024 / 1024} MB`);

    //   const options = {
    //     maxSizeMB: 1,
    //     maxWidthOrHeight: 1920,
    //     useWebWorker: true,
    //   };

    //   try {
    //     const compressedFile = await imageCompression(imageFile, options);
    //     console.log(
    //       "compressedFile instanceof Blob",
    //       compressedFile instanceof Blob
    //     ); // true
    //     console.log(
    //       `compressedFile size ${compressedFile.size / 1024 / 1024} MB`
    //     ); // smaller than maxSizeMB

    //     // await uploadToServer(compressedFile); // write your own logic
    //     console.log(compressedFile);
    //   } catch (error) {
    //     console.log(error);
    //   }
    //   // end browser-image-compression
    // },
  },
};
</script>

<style>
#fileInput {
  display: none;
}
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.my-8 {
  margin-top: 4rem;
  margin-bottom: 4rem;
}
</style>
