<script setup>
async function handleOpenFiles() {
  try {
    const files = await openFiles();

    for (const fileHandle of files) {
      const file = await fileHandle.getFile();
      const fileName = file.name;
      const fileContent = await file.text();

      console.log("File name:", fileName);

      const preparedContent = JSON.parse(fileContent);

      if (!preparedContent.layers) {
        alert(
          `File with name ${fileName} is not a valid Lottie file and will be skipped`
        );
        continue;
      }

      preparedContent.layers = preparedContent.layers.filter(
        (layer) => layer.ind != 12345679
      );

      await saveFile({
        fileName: fileName,
        content: JSON.stringify(preparedContent),
      });
    }
  } catch (error) {
    console.error("Не вдалося відкрити файл:", error);
  }
}

async function openFiles() {
  const files = await window.showOpenFilePicker({
    types: [
      {
        description: "Lottie files",
        accept: {
          "text/plain": [".json"],
        },
      },
    ],
    multiple: true,
  });

  return files;
}

async function saveFile(
  params = {
    fileName: "Animation.json",
    content: null,
  }
) {
  if (!params.content) {
    throw new Error("Content is required");
  }
  const fileHandle = await window.showSaveFilePicker({
    suggestedName: params.fileName,
    types: [
      {
        description: "Saving Lottie files",
        accept: { "text/plain": [".json"] },
      },
    ],
  });

  const writableStream = await fileHandle.createWritable();
  await writableStream.write(params.content);
  await writableStream.close();
}
</script>

<template>
  <h1>LottieLab watermark removing tool</h1>
  <button @click="handleOpenFiles">Upload Lottie Animations</button>
</template>

<style scoped></style>
