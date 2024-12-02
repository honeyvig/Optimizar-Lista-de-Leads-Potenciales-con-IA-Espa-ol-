# Optimizar-Lista-de-Leads-Potenciales-con-IA-Espa-ol-
Tenemos una lista de 6.200 agencias de viajes, y nos interesan específicamente aquellas que organizan viajes a USA, así que necesitamos una forma de filtrarlas.

De cada agencia contamos con:

- Nombre
- Ciudad
- Teléfono
- Email
- Sitio web (en solo 740 casos)

En las empresas que tienen sitio web, va a ser más sencillo chequear si ofrecen viajes a USA, pero en el resto, habría que buscar un perfil de Instagram o quizá un sitio web que no hayan declarado.

Estos leads fueron extraídos de un directorio oficial de 2017, así que es probable que algunas agencias ya no estén en actividad.

Nos gustaría contratar a alguien entre miércoles y jueves para que arranque de inmediato.
====================
To address the task of filtering 6,200 travel agencies for those offering trips to the USA, we can develop a Python solution. This solution will leverage web scraping, APIs, and manual verification strategies to refine the dataset. Here's how you can achieve this:
Steps:

    Data Cleaning:
        Ensure the dataset is clean, removing duplicates or invalid entries.

    Website Checking:
        For agencies with a website, use a web scraper to search for relevant keywords like "viajes a USA," "Estados Unidos," or similar.

    Instagram Search:
        For agencies without a website, use an Instagram scraping API or social media search API to locate profiles and check for relevant keywords.

    Email Validation:
        Validate email addresses using an email-checking API to ensure they are active.

    Manual Verification:
        Flag agencies without any digital presence for manual follow-up.

    Automated Filtering:
        Use natural language processing (NLP) to identify travel-related content linked to the USA.

Python Implementation

import pandas as pd
import requests
from bs4 import BeautifulSoup
from email_validator import validate_email, EmailNotValidError
import re

# Load the dataset
data = pd.read_csv('agencias_viajes.csv')  # Ensure this CSV contains Name, City, Phone, Email, Website columns

# Keywords to search
usa_keywords = ["viajes a USA", "Estados Unidos", "viaje a Estados Unidos", "travel to USA"]

# Function to scrape website content
def check_website_for_usa_trips(url):
    try:
        response = requests.get(url, timeout=10)
        if response.status_code == 200:
            soup = BeautifulSoup(response.content, 'html.parser')
            content = soup.get_text().lower()
            if any(keyword.lower() in content for keyword in usa_keywords):
                return True
    except requests.RequestException:
        return False
    return False

# Function to validate emails
def validate_email_address(email):
    try:
        valid = validate_email(email)
        return valid.email
    except EmailNotValidError:
        return None

# Process the dataset
results = []
for index, row in data.iterrows():
    agency = {
        'Name': row['Name'],
        'City': row['City'],
        'Phone': row['Phone'],
        'Email': row['Email'],
        'Website': row['Website'],
        'Offers_USA_Trips': False
    }
    
    # Check website for agencies with a website
    if pd.notna(row['Website']):
        agency['Offers_USA_Trips'] = check_website_for_usa_trips(row['Website'])
    
    # Validate email
    if pd.notna(row['Email']):
        agency['Email'] = validate_email_address(row['Email'])
    
    results.append(agency)

# Convert results to DataFrame
filtered_data = pd.DataFrame(results)

# Save filtered agencies
filtered_data.to_csv('filtered_agencies.csv', index=False)
print("Filtered agencies saved to 'filtered_agencies.csv'")

Features:

    Web Scraping:
        Scrapes websites for relevant keywords.
    Email Validation:
        Ensures only valid emails are retained.
    Automation with APIs:
        Use APIs for Instagram and other platforms to find additional digital presence.

Tools to Enhance Automation:

    Web Scraping: requests, BeautifulSoup, or Scrapy
    Instagram API: Use tools like Instaloader or Graph API.
    Natural Language Processing (NLP): Use OpenAI or Hugging Face models for content analysis.

Next Steps:

    Hire a developer with expertise in data scraping and NLP to build and run this solution.
    Prepare a manual verification team for flagged cases.

This approach will streamline the filtering process, ensuring you identify agencies offering trips to the USA efficiently.
