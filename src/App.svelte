<script>
  import Compressor from "compressorjs";

  let files;
  let compressed;
  let loading = false;
  let quality = 50;
  function downloadImage() {
    if (compressed) {
      const a = document.createElement("a");
      a.href = URL.createObjectURL(compressed);
      a.download = files[0].name;
      a.click();
    }
  }

  function compressImage(file) {
    loading = true;
    new Compressor(file, {
      quality: quality / 100,
      success(result) {
        compressed = result;
        loading = false;
      },
      error(err) {
        console.error(err.message);
      },
    });
  }

  $: fileSize = files ? Math.ceil(files[0].size / 1024) : 0;
  $: compressedFileSize = compressed ? Math.ceil(compressed.size / 1024) : 0;
</script>

{#if loading}
  <section
    class="text-slate-50 font-semibold flex justify-center items-center bg-slate-900 bg-opacity-75 fixed w-screen h-screen top-0 left-0"
  >
    Loading ...
  </section>
{/if}
<main class="p-4 bg-slate-900 min-h-screen text-white flex flex-col gap-4">
  <section class="flex flex-col items-center justify-center gap-1">
    <label for="quality" class="font-semibold">Kualitas Kompresi</label>
    <input type="range" min="1" max="100" bind:value={quality} class="w-1/2" />
    <div>
      <input
        type="number"
        id="quality"
        min="1"
        max="100"
        bind:value={quality}
        class="text-center bg-transparent border-none w-12"
      />
      <span>%</span>
    </div>
    <label
      for="image"
      class="cursor-pointer bg-slate-50 text-slate-800 px-4 py-2 mt-2 font-medium"
      >{files ? "Ganti Foto ..." : "Unggah Foto ..."}</label
    >
    <input
      class="hidden"
      bind:files
      type="file"
      id="image"
      accept=".jpg,.jpeg,.png"
    />
  </section>
  <section class="grid md:grid-cols-2 gap-4">
    {#if files}
      <aside class="flex flex-col gap-2">
        <button
          on:click={() => compressImage(files[0])}
          class="bg-slate-50 text-slate-800 font-medium px-2 py-1"
          >Kompres Foto (Dari: {fileSize > 1024
            ? `${(fileSize / 1024).toFixed(2)} MB`
            : `${fileSize} KB`})</button
        >
        <img src={URL.createObjectURL(files[0])} alt="original" />
      </aside>
    {/if}
    {#if compressed}
      <aside class="flex flex-col gap-2">
        <button
          on:click={downloadImage}
          class="bg-slate-50 text-slate-800 font-medium px-2 py-1"
          >Unduh Hasil Kompresi
          <span>
            {compressedFileSize > 1024
              ? `${(compressedFileSize / 1024).toFixed(2)} MB`
              : `${compressedFileSize} KB`}
          </span>
        </button>
        <img src={URL.createObjectURL(compressed)} alt="compressed" />
      </aside>
    {/if}
  </section>
</main>

<style>
  input[type="number"]::-webkit-inner-spin-button,
  input[type="number"]::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }
</style>
