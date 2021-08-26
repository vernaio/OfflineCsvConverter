<template>
  <div
    class="
      relative
      mt-2
      flex
      justify-center
      px-6
      pt-5
      pb-6
      border-2 border-gray-300 border-dashed
      rounded-md
    "
    :class="{
      'border-blue-500 bg-gray-50': fileOverDropArea,
      'border-gray-500': !fileOverDropArea,
    }"
    @drop.prevent="droppedFiles"
    @dragover.prevent
    @dragenter.prevent="dragenter"
    @dragleave.prevent="dragleave"
  >
    <div class="space-y-1 text-center">
      <svg
        :class="{
          'text-blue-500': fileOverDropArea,
          'text-gray-400': !fileOverDropArea,
        }"
        class="mx-auto h-12 w-12"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
        aria-hidden="true"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"
        />
      </svg>

      <div class="text-sm text-gray-600 justify-center">
        <label
          for="file-upload"
          class="
            relative
            cursor-pointer
            rounded-md
            font-semibold
            text-blue-300
            hover:text-blue-500
            focus-within:outline-none
          "
        >
          <span>Select file</span>
          <input
            id="file-upload"
            name="file-upload"
            @change="selectedFiles"
            type="file"
            ref="fileupload"
            class="sr-only"
            accept="accept"
          />
        </label>
        <p class="">or</p>
        <p class="font-semibold">Drag & Drop HERE</p>
        <div
          v-if="text !== null"
          class="text-gray-500 text-center mt-2 text-xs"
        >
          {{ text }}
        </div>

        <!--Errors-->
        <div class="text-red-500 mt-1 flex justify-end text-xs font-bold">
          <div>{{ message !== null ? message : " " }}</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  components: {},

  props: ["accept", "text"],

  data() {
    return {
      message: null,
      filename: this.initialFilename,
      progress: this.initialProgress,
      fileOverDropArea: false,
      file: undefined,
      dragcount: 0,
      pending: false,
    };
  },

  methods: {
    droppedFiles(e) {
      let droppedFiles = e.dataTransfer.files;
      if (!droppedFiles) {
        this.fileOverDropArea = false;
        return;
      }
      //convert to array
      if (typeof droppedFiles === "object") {
        droppedFiles = [droppedFiles[0]];
      }
      let files = this.filterByAccept(droppedFiles);
      this.filesSelected(files);
      this.fileOverDropArea = false;
    },

    selectedFiles(event) {
      let unfilteredFiles = event.target.files;
      //convert to array
      if (typeof unfilteredFiles === "object") {
        unfilteredFiles = [unfilteredFiles[0]];
      }
      let files = this.filterByAccept(unfilteredFiles);
      this.filesSelected(files);
      this.$refs.fileupload.value = null;
    },

    filesSelected(files) {
      this.$emit("file-selected", files[0]);
    },

    dragenter() {
      this.dragcount++;
      this.fileOverDropArea = true;
    },

    dragleave() {
      this.dragcount--;
      if (this.dragcount <= 0) this.fileOverDropArea = false;
    },

    filterByAccept(droppedFiles) {
      this.message = null;
      let allowedFileExtensions = [];
      let allowedFileTypes = [];

      this.accept.split(",").forEach((item) => {
        let entry = item.trim();
        if (entry[0] === ".") allowedFileExtensions.push(entry);
        if (entry.indexOf("application") == 0) allowedFileTypes.push(entry);
      });

      let files = [];
      droppedFiles.forEach((file) => {
        //check file type
        if (allowedFileTypes.includes(file.type)) {
          //check file extension
          let fileExt = this.getFileExtension(file.name);
          if (allowedFileExtensions.includes(fileExt)) {
            //Success
            files.push(file);
          }
          //Failed
          else {
            this.message =
              "Skipped file because file extension is wrong: " +
              file.name +
              ".";
          }
        }
        //Failed
        else {
          this.message =
            "Skipped file because filetype is wrong: " + file.name + ".";
        }
      });
      return files;
    },

    getFileExtension(filename) {
      return "." + filename.split(".").pop();
    },
  },
};
</script>