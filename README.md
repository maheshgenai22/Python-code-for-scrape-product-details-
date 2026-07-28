# Python-code-for-scrape-product-details-
Python code for scrape product details 

import requests
from bs4 import BeautifulSoup
from urllib.parse import quote

def scrape_mdcomputers(search_term):
    url = f"https://mdcomputers.in/?route=product/search&search={quote(search_term)}"

    headers = {
        "User-Agent": "Mozilla/5.0"
    }

    response = requests.get(url, headers=headers, timeout=20)
    response.raise_for_status()

    soup = BeautifulSoup(response.text, "html.parser")

    products = []

    for product in soup.select(".product-layout"):
        name = product.select_one(".caption h4 a")
        price = product.select_one(".price")
        image = product.select_one("img")

        products.append({
            "Name": name.get_text(strip=True) if name else "N/A",
            "Price": price.get_text(" ", strip=True) if price else "N/A",
            "Product URL": name["href"] if name else "N/A",
            "Image URL": image["src"] if image else "N/A"
        })

    return products


if __name__ == "__main__":
    term = input("Enter search term: ")

    results = scrape_mdcomputers(term)

    if not results:
        print("No products found.")
    else:
        for i, product in enumerate(results, 1):
            print(f"\nProduct {i}")
            print("-" * 40)
            for key, value in product.items():
                print(f"{key}: {value}")
