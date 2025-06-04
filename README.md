import os

def generate_possible_quarter_formats(quarter, year):
    """Generate possible folder name formats for given quarter and year."""
    q = quarter.upper().replace(" ", "").replace("_", "").replace("-", "")
    y = str(year)
    yy = y[-2:]

    formats = [
        f"{q}{yy}",      # Q125 or Q225
        f"{y}Q{q[1]}",    # 2025Q1
        f"{q}_{yy}",     # Q1_25
        f"{q}_{y}",      # Q1_2025
        f"{yy}Q{q[1]}",   # 25Q1
        f"{q[1]}Q{yy}",   # 1Q25
    ]
    return set(formats)

def find_monitoring_pdf(master_folder, model_id, quarter, year):
    quarter_formats = generate_possible_quarter_formats(quarter, year)

    for root, dirs, files in os.walk(master_folder):
        # Step 1: Look for folders with model ID in their name
        if model_id.lower() in os.path.basename(root).lower():
            for dir_name in dirs:
                if "monitoring" in dir_name.lower():
                    monitoring_path = os.path.join(root, dir_name)

                    # Step 2: Look for any subfolder inside monitoring matching quarter formats
                    for subfolder in os.listdir(monitoring_path):
                        if subfolder in quarter_formats:
                            quarter_path = os.path.join(monitoring_path, subfolder)
                            if os.path.isdir(quarter_path):
                                # Step 3: Look for PDF with "MON" in name
                                for file in os.listdir(quarter_path):
                                    if file.lower().endswith(".pdf") and "mon" in file.lower():
                                        return os.path.join(quarter_path, file)
    return None

# === Example Usage ===
master_folder_path = "/path/to/master/folder"  # Replace this
model_id_input = "12345"
quarter_input = "Q1"   # User input: quarter
year_input = 2025      # User input: year

pdf_path = find_monitoring_pdf(master_folder_path, model_id_input, quarter_input, year_input)

if pdf_path:
    print(f"PDF found: {pdf_path}")
else:
    print("No matching PDF found.")
