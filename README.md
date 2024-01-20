# GITformulario1
Cuenta edirsito code.gs:

const DATA_ENTRY_SHEET_NAME = "Sheet1";
var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(DATA_ENTRY_SHEET_NAME);

function doPost(request = {}) {
    const { postData: { contents, type } = {} } = request;
    var data = parseFormData(contents);
    appendToGoogleSheet(data);
    return ContentService.createTextOutput(JSON.stringify({status: "success", data: data}))
        .setMimeType(ContentService.MimeType.JSON);
}

function parseFormData(postData) {
    var data = {};
    if (postData) {
        var parameters = postData.split('&');
        for (var i = 0; i < parameters.length; i++) {
            var keyValue = parameters[i].split('=');
            data[keyValue[0]] = decodeURIComponent(keyValue[1].replace(/\+/g, ' '));
        }
    }
    return data;
}

function appendToGoogleSheet(data) {
    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    var rowData = headers.map(header => {
        // Especial manejo para el checkbox 'Acepta'
        if (header === 'Acepta') {
            return data[header] ? 'Sí' : 'No';
        }
        return data[header] || '';
    });
    sheet.appendRow(rowData);
}

