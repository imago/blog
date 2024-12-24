---
title: "Migrate Passwords from Strongbox to Apple's Passwords App"
date: 2024-12-24T12:51:11+01:00
draft: false
---

With iOS 18, Apple introduced the **Passwords** app, a built-in password manager that replaces the need for a third-party password manager within the Apple ecosystem, especially when sharing passwords with family. This new functionality allows seamless password sharing among family members, something that wasn't easily achievable before. Having used **Strongbox** on my iOS device and paid for it, I decided it was time to switch to the new **Passwords** app.

However, I found no direct solution to migrate from a KeePass database (kdbx format) to Apple's Passwords app that met my needs—especially one that could handle exporting custom fields and using an additional keyfile. Therefore, I've created this guide to help you write your own converter based on the information found [here](https://p3g3.de/2024/10/keepass-datei-in-apple-passwords-importieren/).

1. **Open the Strongbox App**  
   Open your **Strongbox** app and navigate to the database containing your passwords. Then go to **Settings > Advanced Settings**, click on **Export Database**, and export your current passwords database as a zip file containing the **kdbx** file. The kdbx file (KeePass Database Format) includes all the data stored in your password manager, such as custom fields and attachments.

2. **Set Up the Environment**  
   To create a Python script for the conversion, follow these steps:

   ```bash
   mkdir kdbx2csv
   cd kdbx2csv
   python -m venv venv
   source venv/bin/activate
   pip install pykeepass
   touch kdbx2csv.py
   ```

3. **Create the Python Script**  
   Open the `kdbx2csv.py` file with your favorite text editor and paste the following content:

```python
import csv
import getpass
import sys
from pykeepass import PyKeePass

def transform_kdbx_to_csv(kdbx_file, output_csv, keyfile=None):
    """
    Converts a KeePass database (.kdbx) to a CSV file.
    
    :param kdbx_file: Path to the KeePass database file.
    :param output_csv: Path to save the CSV output file.
    :param keyfile: Optional path to a keyfile for the KeePass database.
    """
    # Prompt user for the KeePass database password
    password = getpass.getpass("Enter KeePass database password: ")

    # Attempt to open the KeePass database
    try:
        kp = PyKeePass(kdbx_file, password=password, keyfile=keyfile)
    except Exception as e:
        print(f"Failed to open KeePass database: {e}")
        return

    # Prepare to write the output CSV
    try:
        with open(output_csv, mode='w', newline='', encoding='utf-8') as outfile:
            fieldnames = ['Title', 'URL', 'Username', 'Password', 'Notes', 'OTPAuth']
            writer = csv.DictWriter(outfile, fieldnames=fieldnames)
            writer.writeheader()

            # Loop through all entries in the KeePass database
            for entry in kp.entries:
                notes = ""
                for key, value in entry.custom_properties.items():
                    notes = notes + key + ": " + value + "\n"
                writer.writerow({
                    'Title': entry.title or '',
                    'URL': entry.url or '',
                    'Username': entry.username or '',
                    'Password': entry.password or '',
                    'Notes': notes,
                    'OTPAuth': ''  # Leaving OTPAuth blank
                })
            print(f"CSV file successfully created at: {output_csv}")
    except Exception as e:
        print(f"Error: Unable to write to output file. Details: {e}")
        sys.exit(1)

# Main function to run the transformation
if __name__ == "__main__":
    import argparse

    parser = argparse.ArgumentParser(description="Convert KeePass database (.kdbx) to a CSV file.")
    parser.add_argument("kdbx_file", help="Path to the KeePass (.kdbx) file.")
    parser.add_argument("output_csv", help="Path to save the output CSV file.")
    parser.add_argument("--keyfile", help="Optional path to the KeePass keyfile.", default=None)
    
    args = parser.parse_args()

    transform_kdbx_to_csv(args.kdbx_file, args.output_csv, args.keyfile)

```

Feel free to customize the script to suit your specific needs.

4. **Activate the Python Environment**  

If you haven't already activated the environment, do so now:
   
```bash
cd kdbx2csv
source venv/bin/activate
```

5. **Place the Files in the Directory**  
   Copy your **kdbx** file (e.g., `my_storage.kdbx`) into the **kdbx2csv** directory, along with the keyfile (e.g., `my_storage.key`) if you have one.

6. **Run the Script**  
   Execute the script using the following commands:

   - With keyfile:
     ```bash
     python3 kdbx2csv.py my_storage.kdbx --keyfile my_storage.key converted_pass.csv
     ```

   - Without keyfile:
     ```bash
     python3 kdbx2csv.py my_storage.kdbx converted_pass.csv
     ```

7. **Import the CSV into Apple Passwords**  
   Now open **Safari**, go to **File > Import from** and select **CSV file with passwords...**. Choose the CSV file you generated (e.g., `converted_pass.csv`).

---

### Customize to Your Needs

Feel free to adjust the script to add features or handle specific cases you may encounter, such as exporting additional fields or handling OTPs. Enjoy using Apple's Passwords app with your migrated data!
