# 📊 Project Data Clean Up using RegEx 🧹

![GitHub](https://img.shields.io/badge/license-MIT-blue)
![GitHub](https://img.shields.io/badge/language-Google%20Apps%20Script-yellow)
![GitHub](https://img.shields.io/badge/status-active-brightgreen)

This project is designed to clean up data in a Google Sheets spreadsheet using **Regular Expressions (RegEx)**. The script is written in **Google Apps Script**, a JavaScript-based language used to automate tasks across Google products.

---

## 🚀 Features

- **📋 User Menu Creation**: A custom menu is added to the Google Sheets UI, allowing users to easily run the data cleaning function.
- **🧼 Data Cleaning**: The script cleans up names, phone numbers, and emails from a specified data sheet and outputs the cleaned data to an output sheet.
- **🔍 RegEx Pattern Matching**: The script uses RegEx to extract and format mobile phone numbers.

---

## 🛠️ How to Use

1. **Open Google Sheets**: Open the Google Sheets document where you want to clean the data.
2. **Open Script Editor**: Go to `Extensions` > `Apps Script` to open the script editor.
3. **Copy and Paste Code**: Copy the provided script and paste it into the script editor.
4. **Save and Run**: Save the script with a name like `DataCleanUp`. Then, run the `onOpen` function to create the custom menu in your Google Sheets.
5. **Use the Menu**: In your Google Sheets, you will now see a new menu item called `Clean Up Data`. Click on it and select `Clean Data` to run the data cleaning function.
6. **Use the Existing sheet to test the code** https://docs.google.com/spreadsheets/d/1jmCqGg-fElPQNxNFO8nalN1iPDgVHdwT9AM4_5Iq4go/edit?usp=sharing

---

## 📝 Code Overview

### 🧩 Menu Creation

```javascript
function onOpen(){
  const ui = SpreadsheetApp.getUi();
  ui.createMenu('Clean Up Data')
    .addItem('Clean Data', 'CleanUp_Data')
    .addToUi();
}
```

### 🧹 Cleaning Up the Data

```javascript
function CleanUp_Data() {

/** 
 ---------------------------------------------------------------------
 //            Get the Sheet References to work with                //
 ---------------------------------------------------------------------
 */  
  var ss =                SpreadsheetApp.getActiveSpreadsheet();
  var data_sheet =        ss.getSheetByName('Data');
  var output_sheet =      ss.getSheetByName('Output');
/** ---------------------------------------------------------------- */

/** 
 ------------------------------------------------------------------------------------
 //                   Get the Data Arrays to work with                             //
 ------------------------------------------------------------------------------------
 */
  var names =     data_sheet.getRange("C2:C").getValues().flat().filter(a => a!='');
  var numbers =   data_sheet.getRange("F2:F").getValues().flat();
  var emails =    data_sheet.getRange("G2:G").getValues().flat();
/** ------------------------------------------------------------------------------- */

/** 
 ------------------------------------------------------------------------------------
 //                    Initialize some variables                                    //
 ------------------------------------------------------------------------------------
 */
  let first_names = [];
  let second_names = [];
  let phones = [];
  let phone = '';

  // Define the RegEx pattern
  let myRegEx = /((Mobile:).\(\d*\).\d*-\d*)/;
  
/** ------------------------------------------------------------------------------- */

/** 
 ------------------------------------------------------------------------------------
 //                    Loop through the data arrays to clean up data               //
 ------------------------------------------------------------------------------------
 */
    for (i = 0; i< names.length; i++){

      // Separate second names and push them in 1D array
      var c_name = names[i].split(", ");
      second_names.push(c_name[0]);
      
      // Separate first names and push them in 1D array
      var first_name = c_name[1].split(" ");
      first_names.push(first_name[0]);

      // use a variable to store the phone number strings
      var str = numbers[i];

      // if string length is not zero then search for the RegEx pattern
      if(str.length != 0){
        
        // Search the phone numbers string for pattern to find mobile numbers
        var match = myRegEx.exec(str);
        
        // if no match is found
        if(match === null){
          
          // then keep the phone string empty and push it in phones array
          phone = ' ';
          phones.push(phone);

        }
        
        else{
          
          phone = match[0];
          // Get the number substring from the complete string i.e. Mobile: (555) 354-5555
          // (555) 354-5555
          phone = phone.substring(8,phone.length);

          // Remove all extra spaces in between
          phone = phone.replaceAll(' ', '');

          // Remove the "()" from the number (555) 354-5555
          phone = phone.replace("(","");
          phone = phone.replace(")","-");
                            
          // push 555-354-5555
          phones.push(phone);
        }

      }
      else{
        phone = ' ';
        phones.push(phone);
      }
      
    }
/** ------------------------------------------------------------------------------- */

/** 
 ------------------------------------------------------------------------------------
 //                  Write Cleaned up Data Arrays to the Output Sheet               //
 ------------------------------------------------------------------------------------
 */
  const double_first_names = first_names.map(item => [item]);
  output_sheet.getRange(2,1,double_first_names.length,1).setValues(double_first_names);

  const double_second_names = second_names.map(item => [item]);
  output_sheet.getRange(2,2,double_second_names.length,1).setValues(double_second_names);
  
  const double_phones = phones.map(item => [item]);
  output_sheet.getRange(2,3,double_phones.length,1).setValues(double_phones);

  const double_emails = emails.map(item => [item]);
  output_sheet.getRange(2,4,double_emails.length,1).setValues(double_emails);
/** ------------------------------------------------------------------------------- */

}  
/**=================================================================================================== */ 
```
