import os
import pandas as pd
from bs4 import BeautifulSoup
from google.oauth2 import service_account
from googleapiclient.discovery import build

# Constants
FOLDER_ID = '1xzRMLjOv9l7G6IGGBtM6Zgb2oUnvOzTs'  # Replace with your folder ID
SCOPES = ['https://www.googleapis.com/auth/drive.readonly']
CREDENTIALS_JSON = 'credentials.json'  # Path to the credentials file

# Authenticate and initialize the Drive API client
credentials = service_account.Credentials.from_service_account_file(
    CREDENTIALS_JSON, scopes=SCOPES)
drive_service = build('drive', 'v3', credentials=credentials)

# Function to list all files in a folder
def list_files_in_folder(folder_id):
    query = f"'{folder_id}' in parents and mimeType='text/html'"
    response = drive_service.files().list(q=query).execute()
    return response.get('files', [])

# Extract data from HTML file content
def extract_data_from_html(file_content):
    soup = BeautifulSoup(file_content, 'html.parser')
    
    # Find the elements containing the required details
    organization_chain = soup.find('div', class_='organization-chain').text.strip()
    tender_title = soup.find('div', class_='tender-title').text.strip()
    bidder_name = soup.find('div', class_='bidder-name').text.strip()
    
    return {
        'Organization Chain': organization_chain,
        'Tender Title': tender_title,
        'Bidder Name': bidder_name
    }

# Main processing
data = []

# List all files in the root folder
files = list_files_in_folder(FOLDER_ID)

for file in files:
    file_id = file['id']
    file_name = file['name']
    
    # Download file content
    request = drive_service.files().get_media(fileId=file_id)
    file_content = request.execute()
    
    # Extract data from the HTML file
    extracted_data = extract_data_from_html(file_content)
    data.append(extracted_data)

# Create a DataFrame from the extracted data
df = pd.DataFrame(data)

# Save the DataFrame to a CSV file
df.to_csv('tender_details.csv', index=False)

print("Data extraction complete. Details saved to 'tender_details.csv'.")
