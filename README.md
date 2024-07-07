# OpenRouter PHP OAuth 2.0 PKCE Authentication Script

This repository contains a PHP script that demonstrates how to implement OAuth 2.0 authorization with Proof Key for Code Exchange (PKCE) using the OpenRouter API. The script facilitates the following steps:

1. Redirecting the user to OpenRouter's authentication page.
2. Handling the redirection from OpenRouter after the user authorizes the application.
3. Exchanging the authorization code for an API key.
4. Fetching and displaying the details of the API key.

## Prerequisites

- PHP installed on your server.
- cURL extension enabled in PHP.
- Sessions enabled in PHP.
- HTTPS enabled on your server.

## Script Breakdown

### Functions

1. **ensure_https()**:
    - Ensures the connection is secure by checking if the request is made over HTTPS. If not, it redirects the user to the HTTPS version of the URL.

2. **generate_code_verifier()**:
    - Generates a random code verifier using `random_bytes` and converts it to a hexadecimal string. If `random_bytes` is not available (PHP < 7.0.0), it falls back to using `mt_rand`.

3. **fallback_random_bytes($length)**:
    - Generates a random string of bytes using `mt_rand` for PHP versions < 7.0.0.

4. **generate_code_challenge($code_verifier)**:
    - Creates a code challenge from the code verifier using the S256 method. This involves hashing the code verifier with SHA-256, base64 encoding the result, and making the string URL-safe by replacing certain characters.

5. **store_code_verifier($code_verifier)**:
    - Stores the code verifier in the session and regenerates the session ID to prevent session fixation attacks.

6. **handle_authorization_response()**:
    - Handles the OAuth authorization response. It retrieves the code verifier from the session, sends a POST request to exchange the authorization code for an API key, and returns the API key if successful.

7. **store_api_key($api_key)**:
    - Stores the API key in the session.

8. **fetch_api_key_details($api_key)**:
    - Fetches and displays the API key details from OpenRouter using a GET request.

9. **generate_auth_url($callback_url, $code_challenge)**:
    - Constructs the authentication URL with the provided callback URL and code challenge, and outputs a link for the user to click.

### Main Script Logic

1. **Ensure HTTPS Connection**:
    ```php
    ensure_https();
    ```
    - This function ensures that the connection is secure by checking if the request is made over HTTPS. If not, it redirects the user to the HTTPS version of the URL.

2. **Check for Authorization Code**:
    ```php
    if (isset($_GET['code'])) {
        $api_key = handle_authorization_response();
        if ($api_key !== null) {
            store_api_key($api_key);
            fetch_api_key_details($api_key);
        }
    }
    ```
    - The script checks if the `code` parameter is present in the URL query string, which indicates that the user has been redirected back after authorization.
    - If the authorization code is present, the `handle_authorization_response()` function is called to exchange the code for an API key and store it in the session.

3. **Start OAuth 2.0 PKCE Flow**:
    ```php
    $callback_url = 'https://YOUR_SECURE_SITE_URL';
    $code_verifier = generate_code_verifier();
    store_code_verifier($code_verifier);
    $code_challenge = generate_code_challenge($code_verifier);
    generate_auth_url($callback_url, $code_challenge);
    ```
    - **Define Callback URL**: Specifies the URL to which the user will be redirected after authentication.
    - **Generate Code Verifier**: Creates a random code verifier using the `generate_code_verifier()` function.
    - **Store Code Verifier**: Stores the code verifier in the session and regenerates the session ID using the `store_code_verifier()` function to prevent session fixation attacks.
    - **Generate Code Challenge**: Creates a code challenge from the code verifier using the `generate_code_challenge()` function.
    - **Generate Authentication URL**: Constructs the authentication URL with the callback URL, code challenge, and code challenge method using the `generate_auth_url()` function. This URL is displayed as a link for the user to click.

## Installation

1. Copy paste the script or download it or clone etc...

2. Modify the `callback_url` in the script to match your site's secure URL:
    ```php
    $callback_url = 'https://YOUR_SECURE_SITE_URL';
    ```
3. Open the script in a web browser. You should see a "Login with OpenRouter" link. Click the link to be redirected to OpenRouter's authentication page. After authorizing the application, you will be redirected back to your site, where the script will exchange the authorization code for an API key and display the API key details.

## Example Usage

1. **Starting the OAuth 2.0 PKCE Flow**:
    - The script will generate a code verifier and code challenge, then display an authentication URL for the user to click.

2. **Handling the Authorization Response**:
    - After the user logs in and authorizes the application, they will be redirected back to the site with an authorization code in the URL. The script will exchange this code for an API key.

3. **Fetching and Displaying API Key Details**:
    - The script will fetch and display details about the obtained API key, such as usage, limit, and rate limits.

## Compatibility

This script is compatible with PHP versions 5.2.0 and later. For PHP versions < 7.0.0, a fallback mechanism using `mt_rand` is provided for generating random bytes.

## License

This project is licensed under the MIT License. See the LICENSE file for details.

## Contributing

Feel free to open issues or submit pull requests to improve this script.

## Acknowledgements

Thanks to the OpenRouter team for providing the API and PKCE in the documentation.
