# InternSpark-Task1
import os
import logging
from datetime import datetime

# Setup logging to file and console
log_file = f'file_operations_{datetime.now().strftime("%Y%m%d_%H%M%S")}.log'
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler(log_file),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

def get_user_input(prompt, default=None):
    """Safe user input with default fallback."""
    try:
        value = input(prompt).strip()
        return value if value else default
    except KeyboardInterrupt:
        logger.info("Operation cancelled by user.")
        return None

def rename_files(directory, prefix):
    """Rename files with prefix, sorted naturally."""
    try:
        files = sorted([f for f in os.listdir(directory) if os.path.isfile(os.path.join(directory, f))])
        for i, file in enumerate(files):
            old_path = os.path.join(directory, file)
            new_name = f"{prefix}_{i+1:03d}_{file}"
            new_path = os.path.join(directory, new_name)
            os.rename(old_path, new_path)
            logger.info(f"Renamed '{file}' -> '{new_name}'")
    except Exception as e:
        logger.error(f"Error during renaming: {e}")

def sort_files_by_ext(directory):
    """Sort files into extension-based folders."""
    try:
        for file in os.listdir(directory):
            file_path = os.path.join(directory, file)
            if os.path.isfile(file_path):
                ext = os.path.splitext(file)[1][1:].lower() or 'other'
                folder_path = os.path.join(directory, ext)
                os.makedirs(folder_path, exist_ok=True)
                new_path = os.path.join(folder_path, file)
                os.rename(file_path, new_path)
                logger.info(f"Moved '{file}' to '{ext}/'")
    except Exception as e:
        logger.error(f"Error during sorting: {e}")

def main():
    print("=== File Automation Script ===")
    directory = get_user_input("Enter directory path: ")
    if not directory or not os.path.exists(directory):
        logger.error(f"Invalid directory: {directory}")
        return

    logger.info(f"Processing directory: {os.path.abspath(directory)}")

    # Rename option
    prefix = get_user_input("Enter rename prefix (Enter to skip): ")
    if prefix:
        rename_files(directory, prefix)

    # Sort option
    if get_user_input("Sort by extension? (y/n): ", "n").lower() == 'y':
        sort_files_by_ext(directory)

    logger.info("All operations completed. Check log file for details.")
    print(f"Log saved to: {log_file}")


if __name__ == "__main__":
    main()
