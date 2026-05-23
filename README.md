// Moje doklady — Google Apps Script backend
// Vlož tento kód do script.google.com a nasaď jako webovou aplikaci

const FILE_NAME = "doklady-data.json";

function getOrCreateFile() {
  const files = DriveApp.getFilesByName(FILE_NAME);
  if (files.hasNext()) return files.next();
  return DriveApp.createFile(FILE_NAME, JSON.stringify({people:[],cars:[],houses:[]}), "application/json");
}

function doGet(e) {
  const file = getOrCreateFile();
  const content = file.getBlob().getDataAsString();
  return ContentService
    .createTextOutput(content)
    .setMimeType(ContentService.MimeType.JSON);
}

function doPost(e) {
  try {
    const data = e.postData.contents;
    JSON.parse(data); // ověření že je to validní JSON
    const file = getOrCreateFile();
    file.setContent(data);
    return ContentService
      .createTextOutput(JSON.stringify({ok: true}))
      .setMimeType(ContentService.MimeType.JSON);
  } catch(err) {
    return ContentService
      .createTextOutput(JSON.stringify({ok: false, error: err.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
