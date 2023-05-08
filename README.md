# crud-google-sheets-restful-api
# Simple CRUD API

-For use Sheets File -> Extensions -> Apps Script <br>
-Add Services "Google Sheets API"<br>
-Simple url api {method} https://script.google.com/macros/s/xxxxxxxxxxxxxxxxxxxxxx/exec?action=getData

```ruby
var ss = SpreadsheetApp.openById('SpreadsheetID');
var sheet = ss.getSheetByName('sheetName ex. Sheet1');

function doGet(e) {
  var action = e.parameter.action;
  switch (action) {
    case 'getData':
      return getData();
    default:
      return ContentService.createTextOutput('Invalid action');
  }
}

function doPost(e) {
  var action = e.parameter.action;
  switch (action) {
    case 'addData':
      return addData(e);
    default:
      return ContentService.createTextOutput('Invalid action');
  }
}

function doPut(e) {
  var action = e.parameter.action;
  switch (action) {
    case 'updateData':
      return updateData(e);
    default:
      return ContentService.createTextOutput('Invalid action');
  }
}

function doDelete(e) {
  var action = e.parameter.action;
  switch (action) {
    case 'deleteData':
      return deleteData(e);
    default:
      return ContentService.createTextOutput('Invalid action');
  }
}


function getData() {
  var rows = sheet.getRange(2, 1, sheet.getLastRow() - 1, sheet.getLastColumn()).getValues();
  var data = [];
  for (var i = 0; i < rows.length; i++) {
    var row = rows[i];
    var record = {};
    record['id'] = row[0];
    record['data'] = row[1];
    data.push(record);
  }
  var result = JSON.stringify(data);
  return ContentService.createTextOutput(result).setMimeType(ContentService.MimeType.JSON);
}

function addData(e) {
  var id = e.parameter.id;
  var data = e.parameter.data;
  var rows = sheet.getDataRange().getValues();
  for (var i = 1; i < rows.length; i++) { // Start from 1 to skip header row
    if (rows[i][0] == id && rows[i][1] == data) {
      return ContentService.createTextOutput('Record already exists');
    }
  }
  sheet.appendRow([id, data]);
  return ContentService.createTextOutput('Data added');
}


function updateData(e) {
  var id = e.parameter.id;
  var data = e.parameter.data;
  var rows = sheet.getDataRange().getValues();
  for (var i = 0; i < rows.length; i++) {
    var row = rows[i];
    if (row[0] == id) {
      sheet.getRange(i + 1, 2).setValue(data);
      return ContentService.createTextOutput('Data updated');
    }
  }
  return ContentService.createTextOutput('Data not found');
}

function deleteData(e) {
  var id = e.parameter.id;
  var rows = sheet.getDataRange().getValues();
  for (var i = 0; i < rows.length; i++) {
    var row = rows[i];
    if (row[0] == id) {
      sheet.deleteRow(i + 1);
      return ContentService.createTextOutput('Data deleted');
    }
  }
  return ContentService.createTextOutput('Data not found');
}
```
