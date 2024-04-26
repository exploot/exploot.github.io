---
author: 
title: File Integrity Monitor
date: 2024-04-26
categories: [Projects]
tags: [Powershell, Security]
toc: true
math: true
mermaid: true

---
# A File Integrity Monitor and Hashing Algorithms -

This project dives into building a simple File Integrity Monitor (FIM) using PowerShell. A FIM plays a crucial role in system security by detecting unauthorized modifications to critical files. These changes could indicate malware infections, tampering attempts, or even accidental edits. Our FIM will leverage the power of SHA-256 encryption to ensure the validity of monitored files. SHA-256 is a cryptographic hash function that generates a unique, fixed-length value (hash) for a given data set. Any change to the data, no matter how small, will result in a completely different hash value. This characteristic makes SHA-256 ideal for file integrity monitoring. Our script will calculate the SHA-256 hash of each monitored file and compare it to a trusted baseline. Any discrepancies between the current hash and the baseline indicate a file modification, allowing you to investigate and address potential security concerns promptly. By implementing this FIM with SHA-256 encryption, you gain a proactive approach to safeguarding your system's integrity. You'll receive information about file changes, enabling you to prevent potential data breaches and maintain a secure computing environment.

The script is divided into several sections. First, we define functions. The 

Calculate-File-Hash function takes a file path and calculates its SHA256 hash using Get-FileHash. It returns the hash object containing both the file path and the calculated hash value. The Erase-Baseline-If-Already-Exists function checks for a baseline file named "baseline.txt" and deletes it if found.

The script then prompts the user to choose between two options:

    Collect new Baseline? This option calculates the SHA256 hash of all files in a specified directory (.\\Files by default) and stores them in a new "baseline.txt" file.
    Begin monitoring files with saved Baseline? This option assumes a baseline file already exists and continuously monitors the files in the same directory for changes.

```
Write-Host ""
Write-Host "What would you like to do?"
Write-Host ""
Write-Host "    1) Collect new Baseline?"
Write-Host "    2) Begin monitoring files with saved Baseline?"
Write-Host ""
$response = Read-Host -Prompt "Please enter '1' or '2'"
Write-Host ""
```

If the user chooses to collect a new baseline, the script first ensures a clean slate by deleting any existing baseline file. Then, it retrieves all files within the target directory and loops through each file. For each file, it calculates the hash and appends the file path and its hash value (separated by a pipe symbol) to the "baseline.txt" file.

When the user opts to monitor with a saved baseline, the script first creates a dictionary to store file paths and their corresponding hashes. It then loads the contents of "baseline.txt" and populates the dictionary with extracted file paths as keys and hash values.

The script then enters a loop that continuously monitors the files. It pauses briefly before retrieving all files again from the target directory. It loops through each file, calculates its hash, and checks if the file path exists in the dictionary. If not found, it indicates a new file has been created, and the script displays a message with the file path.

If the file path exists in the dictionary, the script compares the newly calculated hash with the stored value. If they match, no change is detected. If they differ, it indicates a file modification, and the script displays a message with the file path.

Finally, the script loops through all the keys in the dictionary and checks if the corresponding file still exists. If not found, it means a file from the baseline has been deleted, and the script displays a message indicating the missing file.

This is only a basic example and can (or maybe will!) be further enhanced with features like *logging* the changesm sending notifications, or scheduling automatic monitoring.
And a note if you want to use this code: be sure to replace .\Files with the actual path to the directory containing the files you want to monitor. 


Below is the full code:
```
Function Calculate-File-Hash($filepath) {
    $filehash = Get-FileHash -Path $filepath -Algorithm SHA512
    return $filehash
}
Function Erase-Baseline-If-Already-Exists() {
    $baselineExists = Test-Path -Path .\baseline.txt

    if ($baselineExists) {
        # Delete it
        Remove-Item -Path .\baseline.txt
    }
}


Write-Host ""
Write-Host "What would you like to do?"
Write-Host ""
Write-Host "    1) Collect new Baseline?"
Write-Host "    2) Begin monitoring files with saved Baseline?"
Write-Host ""
$response = Read-Host -Prompt "Please enter '1' or '2'"
Write-Host ""

if ($response -eq "1".ToUpper()) {
    # Delete baseline.txt if it already exists
    Erase-Baseline-If-Already-Exists

    # Calculate Hash from the target files and store in baseline.txt
    # Collect all files in the target folder
    $files = Get-ChildItem -Path .\Files

    # For each file, calculate the hash, and write to baseline.txt
    foreach ($f in $files) {
        $hash = Calculate-File-Hash $f.FullName
        "$($hash.Path)|$($hash.Hash)" | Out-File -FilePath .\baseline.txt -Append
    }
    
}

elseif ($response -eq "2".ToUpper()) {
    
    $fileHashDictionary = @{}

    # Load file|hash from baseline.txt and store them in a dictionary
    $filePathsAndHashes = Get-Content -Path .\baseline.txt
    
    foreach ($f in $filePathsAndHashes) {
         $fileHashDictionary.add($f.Split("|")[0],$f.Split("|")[1])
    }

    # Begin (continuously) monitoring files with saved Baseline
    while ($true) {
        Start-Sleep -Seconds 1
        
        $files = Get-ChildItem -Path .\Files

        # For each file, calculate the hash, and write to baseline.txt
        foreach ($f in $files) {
            $hash = Calculate-File-Hash $f.FullName
            #"$($hash.Path)|$($hash.Hash)" | Out-File -FilePath .\baseline.txt -Append

            # Notify if a new file has been created
            if ($fileHashDictionary[$hash.Path] -eq $null) {
                # A new file has been created!
                Write-Host "$($hash.Path) has been created!" -ForegroundColor Cyan
            }
            else {

                # Notify if a new file has been changed
                if ($fileHashDictionary[$hash.Path] -eq $hash.Hash) {
                    # The file has not changed
                }
                else {
                    # File file has been compromised!, notify the user
                    Write-Host "$($hash.Path) has changed!!!" -ForegroundColor Yellow
                }
            }
        }

        foreach ($key in $fileHashDictionary.Keys) {
            $baselineFileStillExists = Test-Path -Path $key
            if (-Not $baselineFileStillExists) {
                # One of the baseline files must have been deleted, notify the user
                Write-Host "$($key) has been deleted!" -ForegroundColor White -BackgroundColor Black
            }
        }
    }
}
```