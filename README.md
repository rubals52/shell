function Get-FolderSizeReport {
    param (
        [string]$Path,
        [int]$Indent = 0
    )

    # Get size of files in the current folder
    $folderFiles = Get-ChildItem -Path $Path -File -ErrorAction SilentlyContinue
    $folderSize = ($folderFiles | Measure-Object -Property Length -Sum).Sum

    # Print current folder
    $indentStr = ' ' * $Indent
    "{0}{1} - {2:N2} MB" -f $indentStr, (Split-Path $Path -Leaf), ($folderSize / 1MB)

    # List each file in the current folder
    foreach ($file in $folderFiles) {
        $fileSizeMB = $file.Length / 1MB
        "{0}  [File] {1} - {2:N2} MB" -f $indentStr, $file.Name, $fileSizeMB
    }

    # Recurse into subfolders
    $subFolders = Get-ChildItem -Path $Path -Directory -ErrorAction SilentlyContinue
    foreach ($sub in $subFolders) {
        Get-FolderSizeReport -Path $sub.FullName -Indent ($Indent + 2)
    }
}

# Run the function on your target folder
Get-FolderSizeReport -Path "C:\Your\Folder\Path"
