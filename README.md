# wick-downloader

An asynchronous partial EGS downloader. This can be used to download individual asset files without downloading the entire archive.

# API

## WickDownloader

### constructor()

```javascript
const { WickDownloader } = require('wick-downloader');
const wickdl = new WickDownloader();
```

### startService(): void

This function must be executed to initialize the state of the downloader. Internally, this involves setting up the HTTP service and fetching the App and Chunk manifests for the latest version.

```javascript
await wickdl.startService();
```

### getPakNames(): string[]

This returns a list of strings showing the relative path of each pak file that can be accessed.

### getPak(filename: string): Promise\<EncryptedPakContainer\>

Used to initialize an individual pak service. The `filename` argument must be an exact match to one of the entries returned from `getPakNames`. Internally, this downloads the header and index of the pak file.

```javascript
const pakNames = wickdl.getPakNames();
const encryptedPak = await wickdl.getPak(pakNames[0]);
```

### decryptPak(pak: EncryptedPakContainer, key: string): Promise\<PakContainer\>

Decrypts an `EncryptedPakContainer` class to return a usable `PakContainer`

```javascript
const pakService = await wickdl.decryptPak(encryptedPak, "decryption key");
```

### getFileData(pak: PakContainer, filename: string): Promise\<Buffer\>

Downloads an individual asset file specified in `filename` from the `pak` service, returning a `Buffer` with the contents.

```javascript
const fileData = await wickdl.getFileData(pakService, pakService.get_file_names()[0]);
```

### shutdown()

Clears up the internal runtime and callback procedures. This is necessary to finish execution of the script.

## EncryptedPakContainer

This class does not have any functionality, and is a representation of some internal state. Needs to be passed to `decryptPak` to get a usable `PakContainer`

## PakContainer

### get_pak_mount(): string

Returns the mount path for the pak file.

### get_file_names(): string[]

Returns a list of each file that is retrievable from this pak file.

### get_file_hash(filename: string): string

Returns a unique hash of the file specified. Must be an exact match to a filepath from `get_file_names`
