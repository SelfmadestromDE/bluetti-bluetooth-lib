# BLUETTI Bluetooth Channel Encryption and Decryption Module Usage Guide

## 1. Bluetooth Channel Encryption Scheme Description

?To address Bluetooth communication security issues, BLUETTI products implement an encryption scheme for the Bluetooth channel. After establishing a Bluetooth connection, encrypted communication can only proceed using encryption and decryption keys. Therefore, with the encryption feature enabled, plaintext communication is not possible to retrieve data information from BLUETTI products.

To solve this issue, BLUETTI provides a Bluetooth channel encryption/decryption module. The module offers APIs for external calls to enable encrypted communication.

## 2. Using the Bluetooth Encryption and Decryption Module

### 2.1 Installation

The module consists of two files. Place them in your Python installation directory (`site-packages`):

- `bluetti_crypt.py`: Python wrapper for the module.
- `_bluetti_crypt.so` (Linux/MacOS) or `_bluetti_crypt.pyd` (Windows): Core library implementing encryption/decryption.


**Steps**:

1. Download the package:  
 [bluetti-official/bluetti-bluetooth-lib](https://github.com/bluetti-official/bluetti-bluetooth-lib)
2. Unzip the package.
3. Copy files to `site-packages` directory. 

**Linux/MacOS Example**:

?	Unzip the package: 

?	`unzip bluetti_crypt.zip -C bluetti_crypt`

?	Copy files to `site-packages` directory: 

?	`cp -rf bluetti_crypt.py _bluetti_crypt.so /home/cloud/.local/lib/python3.12/site-packages/`

*Replace with your Python path*
4. Copy and encrypt/decrypt the authorization file to the 'Program' runtime directory and name it bluetti_device_licence.csv

### 2.2 Usage

The Bluetooth encryption and decryption module provides three APIs for external calls£º

```c++
/*!
 * @brief Bluetooth encrypted channel connection handling
 * @param data,  input data, data sent from the Bluetti device
 * @param status return status
            0: Initializing
            1~2: Authenticating
            3: Checking SN validity
            4: Bluetooth connection completed
 * @return Returns encrypted data on success, or an empty string on failure""
*/
std::string BluettiCrypt::ble_crypt_link_handler(std::string data, int *status);

/*!
 * @brief Encrypt data
 * @param data, data to be encrypted
 * @return Returns encrypted data on success, or an empty string on failure""
 */
std::string BluettiCrypt::encrypt_data(std::string data);

/*!
 * @brief Decrypt data
 * @param data, data to be decrypted
 * @return Returns decrypted data on success, or an empty string on failure""
 */
std::string BluettiCrypt::decrypt_data(std::string data);

```

- Bluetooth Connection:`ble_crypt_link_handler()`is used during the key authentication phase after Bluetooth connection. Data interaction such as querying data can only proceed after this phase is completed. During the key authentication phase, no additional processing of Bluetooth data is required; simply pass the data through. The Bluetooth encryption and decryption module will directly interact with the Bluetooth device. Proceed to the next step only after`ble_crypt_link_handler`returns`BLE_LINK_STATUS_COMEPLETE`.

- Bluetooth Data Encryption: `encrypt_data()`is the API for encrypting data. Data sent to the Bluetti device must be encrypted to be recognized.
- Bluetooth Data Decryption: `decrypt_data()`is the API for decrypting data. Data received from the Bluetti device is encrypted and must be decrypted to parse the protocol data.

Reference implementations/demos are available for Home-Assistant and the bluetti-mqtt open-source project.