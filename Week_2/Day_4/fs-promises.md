## FS Module
The fs module has a modules property that is compatible with promises. The promise-based FS methods are compatible with await/async AND .then.catch syntax. Here is how to import it:

```javascript
// Using the promises API (either approach works)
const fs = require('fs').promises;
// or
const { promises: fs } = require('fs');
```

1. fs.readFile()

    Purpose: Reads the entire contents of a file.
    Input:
        path: String, Buffer, or URL representing the file path.
        options (optional): Object or string that specifies encoding or flags.
    Return: A promise that resolves with the file's contents.
    Example:

    ```javascript

    fs.readFile('/path/to/file.txt', 'utf8').then(data => console.log(data));
    ```

2. fs.writeFile()

    Purpose: Asynchronously writes data to a file, replacing the file if it already exists.
    Input:
        file: String, Buffer, or URL representing the file path.
        data: String or Buffer of data to write.
        options (optional): Object that specifies encoding, mode, and flag.
    Return: A promise that resolves when the file has been written.
    Example:

    ```javascript

    fs.writeFile('/path/to/file.txt', 'Hello, world!', 'utf8').then(() => console.log('File written'));```

3. fs.readdir()

    Purpose: Reads the contents of a directory.
    Input:
        path: String, Buffer, or URL representing the directory path.
        options (optional): Object or string that specifies encoding.
    Return: A promise that resolves with an array of the names of the files in the directory.
    Example:

    ```javascript

    fs.readdir('/path/to/directory').then(files => console.log(files));```

4. fs.stat()

    Purpose: Retrieves information about the file or directory.
    Input:
        path: String, Buffer, or URL representing the file or directory path.
    Return: A promise that resolves with an fs.Stats object containing information about the file or directory.
    Example:

    ```javascript

    fs.stat('/path/to/file.txt').then(stats => console.log(stats));
    ```
5. fs.unlink()

    Purpose: Asynchronously removes a file or symbolic link.
    Input:
        path: String, Buffer, or URL representing the file path.
    Return: A promise that resolves when the file is removed.
    Example:

    ```javascript

    fs.unlink('/path/to/file.txt').then(() => console.log('File removed'));```

6. fs.mkdir()

    Purpose: Asynchronously creates a directory.
    Input:
        path: String, Buffer, or URL representing the directory path.
        options (optional): Object specifying recursive flag and permissions mode.
    Return: A promise that resolves when the directory is created.
    Example:

    ```javascript

    fs.mkdir('/path/to/new-directory').then(() => console.log('Directory created'));```

7. fs.rmdir()

    Purpose: Asynchronously removes a directory.
    Input:
        path: String, Buffer, or URL representing the directory path.
        options (optional): Object specifying recursive flag.
    Return: A promise that resolves when the directory is removed.
    Example:

    ```javascript

    fs.rmdir('/path/to/directory').then(() => console.log('Directory removed'));```

