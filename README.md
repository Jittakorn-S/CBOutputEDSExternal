# CB Output EDS External

CB Output EDS External is a .NET Framework task application for automatically unzipping files to a designated folder. This tool is designed to be run as a scheduled task to automate the process of unzipping new files and organizing them.

## Features

  * **Automated Unzipping**: Automatically unzips `.zip` files from a source directory.
  * **Folder Creation**: Creates a new folder for each unzipped file, named after the original zip file (without the extension).
  * **Duplicate Handling**: Checks for existing folders to prevent duplication and extracts files into existing folders if they already exist.
  * **Logging**: Creates a log file to record the status of the unzip process, including successes and errors.
  * **File Renaming**: Renames the processed `.zip` files to `.zip.bak` to mark them as completed.
  * **Wafer Mapping Data Processing**: The application has specific logic to handle "Wafer Mapping" data, including updating `LOT.DAT` files with new information based on the unzipped `.DAT` files.

## Flow Process

The application follows this general process:

1.  Unzip files from `\\172.16.0.115\CBALL` to `\\172.16.0.115\WaferMapping`. This is intended to be a daily task for new files.
2.  The task scheduler will run the application to unzip the files to the `WaferMapping` directory.

## How it Works

The application, written in C\#, performs the following steps:

1.  **Reads Configuration**: It starts by reading the paths for the source directory (`PathCBOutput`), destination directory (`PathWaferMapping`), and log file (`Logfile`) from the `App.config` file.
2.  **Gets Zip Files**: It retrieves a list of all files with the `.zip` extension from the source directory.
3.  **Processes Each Zip File**: For each `.zip` file, it does the following:
      * **Creates Extraction Path**: It determines the destination path for the unzipped files.
      * **Handles New and Existing Folders**:
          * If a folder with the same name as the zip file doesn't exist, it creates the folder and extracts the contents of the zip file into it.
          * If the folder already exists, it extracts the files from the zip archive into the existing folder, overwriting any existing files.
      * **Updates Wafer Data**: If the folder already exists, it processes `.DAT` files within the directory to update a `LOT.DAT` file. This involves writing specific byte data to the `LOT.DAT` file based on the number and names of the other `.DAT` files.
      * **Logs Success**: It logs a success message with a timestamp to the specified log file.
      * **Renames Processed Zip**: It renames the original zip file with a `.bak` extension. If a `.bak` file with the same name already exists, it appends a timestamp to the new `.bak` file name.
4.  **Error Handling**: The application includes `try-catch` blocks to handle potential errors during the unzipping and file renaming processes. Any errors are logged to the log file with a timestamp and error message.

## Configuration

You can configure the application by editing the `App.config` file.

```xml
<configuration>
	<appSettings>
		<add key="PathCBOutput" value="C:\Users\Desktop\CBOutput\BACKUP"/>
		<add key="PathWaferMapping" value="C:\Users\Desktop\WaferMapping\"/>
		<add key="Logfile" value="C:\Users\Desktop\log\LogFile.txt"/>
	</appSettings>
</configuration>
```

  * `PathCBOutput`: The directory where the application looks for `.zip` files.
  * `PathWaferMapping`: The directory where the application extracts the contents of the `.zip` files.
  * `Logfile`: The full path to the log file.

## Installation

To use this application, create a task scheduler to run the executable. The task should be configured to run at your desired frequency (e.g., daily).
