
# AI Model Development for Fitness Warehouse

## Overview

This project aims to develop an AI model to optimize quality control (QC) processes, automate manual checks, and ensure high standards of product verification for fitnesssuperstore.com. The code is structured to address multiple steps in the QC process, from verifying order inclusions to performing color and condition checks.

## Steps and Code Implementation

### Step 0: Verify Order Inclusions

1. **Authenticate and Access Google Sheets:**
    - Uses `gspread` and `oauth2client` to access the Google Sheets API.
    - Retrieves records from the specified Google Sheet and converts them into a pandas DataFrame.

2. **Verify Order Inclusions:**
    - Filters pending orders from the DataFrame and displays them.

### Step 1: Locate and Define QC Pictures

1. **Organize QC Pictures:**
    - Organizes QC pictures into directories based on product codes.
    - Copies pictures to appropriate directories for easier access and SOP creation.

### Step 2: Scan Assembly Manuals

1. **Extract Text from PDFs:**
    - Uses `PyPDF2` to extract text from assembly manuals.
    - The extracted text is used to verify product information and identify discrepancies.

### Step 3: Verify Data Boards and Google Drive Sheets

1. **Extract Text from Images:**
    - Uses `pytesseract` for Optical Character Recognition (OCR) to extract text from images of data boards.

### Step 4: Check Product Pages and Historical Data

1. **Cross-Reference Data:**
    - Compares product data against historical data to identify discrepancies in accessories, specifications, and other details.

## How to Use the Code

1. **Setup:**
    - Install necessary libraries using pip:
      ```sh
      !pip install gspread pandas oauth2client PyPDF2 pytesseract pillow
      ```
    - Authenticate with Google Sheets using a service account JSON file.

2. **Run the Code:**
    - Follow the steps outlined in the code to verify order inclusions, organize QC pictures, scan assembly manuals, verify data boards, and cross-reference product pages.

3. **Extend the Code:**
    - Additional steps such as performing color and condition checks, evolving the AI model, and automating the picture gathering process can be implemented following the provided structure.

## Example Usage

### Verifying Order Inclusions
```python
# Authenticate and access Google Sheets
import gspread
from oauth2client.service_account import ServiceAccountCredentials
import pandas as pd

scope = ["https://spreadsheets.google.com/feeds", 'https://www.googleapis.com/auth/spreadsheets',
         "https://www.googleapis.com/auth/drive.file", "https://www.googleapis.com/auth/drive"]

creds = ServiceAccountCredentials.from_json_keyfile_name('path_to_your_service_account.json', scope)
client = gspread.authorize(creds)
sheet = client.open('your_google_sheet_name')
sheet_instance = sheet.get_worksheet(0)
records_data = sheet_instance.get_all_records()
records_df = pd.DataFrame.from_dict(records_data)

# Filter pending orders
pending_orders = records_df[records_df['Order Status'] == 'Pending']
print(pending_orders)
```

### Organizing QC Pictures
```python
import os
import shutil

def organize_qc_pictures(image_dir, output_dir):
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)
    
    for image_file in os.listdir(image_dir):
        if image_file.endswith('.jpg') or image_file.endswith('.png'):
            product_code = image_file.split('_')[0]
            product_dir = os.path.join(output_dir, product_code)
            
            if not os.path.exists(product_dir):
                os.makedirs(product_dir)
            
            shutil.copy(os.path.join(image_dir, image_file), product_dir)

image_directory = '/path/to/qc_pictures'
output_directory = '/path/to/organized_qc_pictures'
organize_qc_pictures(image_directory, output_directory)
```

### Extracting Text from Assembly Manuals
```python
import PyPDF2

def extract_text_from_pdf(pdf_path):
    pdf_file = open(pdf_path, 'rb')
    pdf_reader = PyPDF2.PdfFileReader(pdf_file)
    text = ""
    
    for page_num in range(pdf_reader.numPages):
        page = pdf_reader.getPage(page_num)
        text += page.extractText()
    
    pdf_file.close()
    return text

assembly_manual_path = '/path/to/assembly_manual.pdf'
manual_text = extract_text_from_pdf(assembly_manual_path)
print(manual_text)
```

### Extracting Text from Data Boards
```python
import pytesseract
from PIL import Image

def extract_text_from_image(image_path):
    image = Image.open(image_path)
    text = pytesseract.image_to_string(image)
    return text

data_board_image_path = '/path/to/data_board_image.jpg'
data_board_text = extract_text_from_image(data_board_image_path)
print(data_board_text)
```

### Cross-Referencing Data
```python
def cross_reference_data(product_data, historical_data):
    discrepancies = []
    for key in product_data.keys():
        if product_data[key] != historical_data.get(key, None):
            discrepancies.append((key, product_data[key], historical_data.get(key, None)))
    return discrepancies

product_data = {
    'ProductID': '12345',
    'Accessory': 'Cable',
    'Specification': 'Length: 2m'
}

historical_data = {
    'ProductID': '12345',
    'Accessory': 'Cable',
    'Specification': 'Length: 1.5m'
}

discrepancies = cross_reference_data(product_data, historical_data)
print(discrepancies)
```

## Next Steps

- Implement the remaining steps such as performing color and condition checks, evolving the AI model, and automating the picture gathering process.
- Continuously test and refine the AI model to improve its accuracy and efficiency in QC processes.


