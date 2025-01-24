import getpass  # For securely entering passwords

class Product:
    def __init__(self, id, name, price, stock):
        self.id = id
        self.name = name
        self.price = price
        self.stock = stock  # New attribute for stock

    def __str__(self):
        return f"{self.id}. {self.name} - ${self.price} (Stock: {self.stock})"

    def reduce_stock(self, quantity=1):
        """Reduces the stock when an item is added to the cart."""
        if self.stock >= quantity:
            self.stock -= quantity
            return True
        return False

    def restock(self, quantity=1):
        """Restocks the product."""
        self.stock += quantity


class Cart:
    def __init__(self):
        self.items = []

    def add_to_cart(self, product, quantity=1):
        """Add a product to the cart, checking stock availability."""
        if product.reduce_stock(quantity):
            self.items.append({'product': product, 'quantity': quantity})
            print(f"Added {quantity} {product.name}(s) to the cart.")
        else:
            print(f"Sorry, not enough stock for {product.name}.")

    def remove_from_cart(self, product_id):
        """Remove a product from the cart."""
        product = next((item for item in self.items if item['product'].id == product_id), None)
        if product:
            self.items.remove(product)
            product['product'].restock(product['quantity'])
            print(f"Removed {product['product'].name} from the cart.")
        else:
            print(f"Product with ID {product_id} not found in the cart.")

    def view_cart(self):
        """View all items in the cart."""
        if not self.items:
            print("Your cart is empty.")
        else:
            print("\nYour Cart:")
            for item in self.items:
                print(f"{item['product'].name} - ${item['product'].price} x {item['quantity']}")
            total = sum([item['product'].price * item['quantity'] for item in self.items])
            print(f"Total: ${total:.2f}")

    def checkout(self):
        """Simulate the checkout process."""
        total = sum([item['product'].price * item['quantity'] for item in self.items])
        print("\nProceeding to checkout...")
        print(f"Total amount due: ${total:.2f}")
        payment = float(input("Enter payment amount: $"))
        if payment >= total:
            print(f"Payment successful! Change: ${payment - total:.2f}")
            self.items.clear()  # Clear cart after checkout
        else:
            print("Insufficient payment. Try again.")


class User:
    def __init__(self, username, password):
        self.username = username
        self.password = password  # In a real app, passwords should be hashed

    def check_password(self, password):
        return self.password == password


class Store:
    def __init__(self):
        self.products = [
            Product(1, "Laptop", 999.99, 10),
            Product(2, "Smartphone", 499.99, 5),
            Product(3, "Headphones", 89.99, 20),
            Product(4, "Smartwatch", 199.99, 15)
        ]
        self.users = [
            User("admin", "password123"),  # Sample admin user
            User("customer", "mypassword")  # Sample customer user
        ]
        self.logged_in_user = None

    def display_products(self):
        """Display all available products."""
        print("\nAvailable Products:")
        for product in self.products:
            print(product)

    def find_product_by_id(self, product_id):
        """Find a product by its ID."""
        return next((prod for prod in self.products if prod.id == product_id), None)

    def login(self):
        """Login function."""
        username = input("Enter username: ")
        password = getpass.getpass("Enter password: ")  # Use getpass to hide the password input
        user = next((u for u in self.users if u.username == username), None)
        if user and user.check_password(password):
            self.logged_in_user = user
            print(f"Welcome, {username}!")
        else:
            print("Invalid credentials. Please try again.")

    def register_user(self, username, password):
        """Register a new user."""
        if any(u.username == username for u in self.users):
            print("Username already exists!")
            return False
        self.users.append(User(username, password))
        print(f"User {username} registered successfully.")
        return True


def main():
    store = Store()
    cart = Cart()

    # Login
    print("Welcome to the Simple E-Commerce App!")
    store.login()

    while True:
        print("\n--- E-Commerce Application ---")
        print("1. View Products")
        print("2. Add Product to Cart")
        print("3. Remove Product from Cart")
        print("4. View Cart")
        print("5. Checkout")
        print("6. Logout")
        print("7. Exit")

        choice = input("Enter your choice (1-7): ")

        if choice == '1':
            store.display_products()

        elif choice == '2':
            product_id = int(input("Enter the product ID to add to cart: "))
            quantity = int(input("Enter the quantity: "))
            product = store.find_product_by_id(product_id)
            if product:
                cart.add_to_cart(product, quantity)
            else:
                print("Invalid product ID!")

        elif choice == '3':
            product_id = int(input("Enter the product ID to remove from cart: "))
            cart.remove_from_cart(product_id)

        elif choice == '4':
            cart.view_cart()

        elif choice == '5':
            cart.checkout()

        elif choice == '6':
            print("Logging out...")
            store.logged_in_user = None

        elif choice == '7':
            print("Thank you for using our e-commerce system!")
            break

        else:
            print("Invalid choice! Please try again.")


if __name__ == "__main__":
    main()
