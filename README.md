# Amazon Scraper

This project is an Amazon scraper that logs into Amazon, navigates to specified best seller categories, and scrapes product details such as name, review, price, and link. The scraped data is saved to a JSON file.

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Features](#features)
- [Contributing](#contributing)
- [License](#license)

## Installation

1. **Clone the repository**:
    ```sh
    git clone https://github.com/Shanmukh666666/amazon-scraper.git
    cd amazon-scraper
    ```

2. **Create and activate a virtual environment**:
    ```sh
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```

3. **Install required packages**:
    ```sh
    pip install -r requirements.txt
    ```

4. **Install ChromeDriver**:
    - Download ChromeDriver from [here](https://sites.google.com/a/chromium.org/chromedriver/downloads) and ensure it matches your Chrome version.
    - Place the `chromedriver.exe` in a directory and update the `driver_path` in the script.

## Usage

1. **Configure your environment variables**:
    - Create a `.env` file in the root directory of your project.
    - Add your Amazon credentials to the `.env` file:
        ```env
        PHONE=your_amazon_phone
        PASSWORD=your_amazon_password
        ```

2. **Run the script**:
    ```sh
    python script.py
    ```

3. **Check the output**:
    - The scraped data will be saved in a file named `all_best_sellers.json`.

## Configuration

- **Headless Mode**: The script runs in headless mode by default. To change this, modify the `create_driver` function's `headless` parameter.
- **Categories**: You can update the list of categories to scrape in the `main` function.

## Features

- **Automated Login**: Logs into Amazon using provided credentials.
- **CAPTCHA Handling**: Detects and handles CAPTCHA by switching to non-headless mode.
- **Scraping**: Extracts product details including name, review, price, and link from best seller pages.
- **Data Saving**: Saves the scraped data to a JSON file.

## Category URLs Used

The following category URLs are used in the script to scrape the best seller products from Amazon:

1. [Kitchen](https://www.amazon.in/gp/bestsellers/kitchen/ref=zg_bs_nav_kitchen_0)
2. [Shoes](https://www.amazon.in/gp/bestsellers/shoes/ref=zg_bs_nav_shoes_0)
3. [Computers](https://www.amazon.in/gp/bestsellers/computers/ref=zg_bs_nav_computers_0)
4. [Electronics](https://www.amazon.in/gp/bestsellers/electronics/ref=zg_bs_nav_electronics_0)
5. [Books](https://www.amazon.in/gp/bestsellers/books/ref=zg_bs_nav_books_0)
6. [Toys](https://www.amazon.in/gp/bestsellers/toys/ref=zg_bs_nav_toys_0)
7. [Apparel](https://www.amazon.in/gp/bestsellers/apparel/ref=zg_bs_nav_apparel_0)
8. [Beauty](https://www.amazon.in/gp/bestsellers/beauty/ref=zg_bs_nav_beauty_0)
9. [Home](https://www.amazon.in/gp/bestsellers/home/ref=zg_bs_nav_home_0)
10. [Health & Personal Care](https://www.amazon.in/gp/bestsellers/hpc/ref=zg_bs_nav_hpc_0)

## Handling CAPTCHA Detection

If a CAPTCHA is detected during the login process, the script handles it by switching from headless mode to non-headless mode to allow manual solving of the CAPTCHA. Here is the relevant part of the script that deals with CAPTCHA detection:

```python
try:
    WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.CLASS_NAME, "a-box"))
    )
    logging.info("CAPTCHA detected. Please solve the CAPTCHA manually.")
    # Save cookies to reuse in non-headless mode
    cookies = driver.get_cookies()
    driver.quit()

    # Open browser in non-headless mode for CAPTCHA solving
    driver = create_driver(headless=False)
    driver.get("https://www.amazon.in/ap/signin")
    for cookie in cookies:
        driver.add_cookie(cookie)
    driver.refresh()

    # Re-enter credentials in non-headless mode
    WebDriverWait(driver, 30).until(
        EC.presence_of_element_located((By.ID, "ap_email"))
    )
    phone_input = driver.find_element(By.ID, "ap_email")
    phone_input.send_keys(phone)
    driver.find_element(By.ID, "continue").click()

    WebDriverWait(driver, 30).until(
        EC.presence_of_element_located((By.ID, "ap_password"))
    )
    password_input = driver.find_element(By.ID, "ap_password")
    password_input.send_keys(password)
    driver.find_element(By.ID, "signInSubmit").click()

    input("Press Enter after solving the CAPTCHA and logging in manually...")

    # Save cookies again after CAPTCHA is solved
    cookies = driver.get_cookies()

    # Quit non-headless mode and switch back to headless mode
    driver.quit()
    driver = create_driver(headless=True)
    driver.get("https://www.amazon.in")
    for cookie in cookies:
        driver.add_cookie(cookie)
    driver.refresh()

except:
    logging.info("No CAPTCHA detected")

Conclusion:
This Amazon Scraper project is a powerful tool for automating the process of logging into Amazon, navigating to best seller categories, and scraping product details. By leveraging Selenium for web automation and BeautifulSoup for HTML parsing, the script provides a seamless way to collect valuable product information. The scraped data is organized and saved in a JSON file, making it easy to analyze or use for further processing.

Precautions:
While using this scraper, it's important to keep the following precautions in mind:

Respect Amazon's Terms of Service:

Ensure that your scraping activities comply with Amazon's terms of service. Excessive scraping or violating their policies could lead to your account being banned.
Handle CAPTCHA Carefully:

CAPTCHA is used by Amazon to prevent automated access. The script includes a mechanism to detect and handle CAPTCHA by switching to non-headless mode, but manual intervention is required to solve it. Be prepared to handle this step.
Use Environment Variables Securely:

Store your Amazon credentials securely in a .env file and avoid hardcoding sensitive information directly in the script. Ensure that the .env file is not shared or uploaded to version control systems.
Update ChromeDriver:

Keep your ChromeDriver updated to match the version of your installed Chrome browser. This helps in avoiding compatibility issues.
Monitor for Changes on Amazon Pages:

Amazon's website structure may change, which could break the scraper. Regularly monitor the script's performance and update the selectors as needed.
Rate Limiting and IP Blocking:

Be mindful of Amazon’s rate limits to avoid IP blocking. Implement delays between requests if necessary, and consider using proxies to distribute the load.
Error Handling:

The script includes error handling to capture screenshots and save page sources when issues occur. Review these logs to debug and improve the scraper.
