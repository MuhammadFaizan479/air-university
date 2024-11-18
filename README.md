//# air-university
//air university python code
class User:
    def __init__(self, username, password, role):
        self.username = username
        self.password = password
        self.role = role  # "Admin" or "User"

    def authenticate(self, username, password):
        return self.username == username and self.password == password

    def change_password(self, old_password, new_password):
        if self.password == old_password:
            self.password = new_password
            return True
        else:
            return False


class Product:
    def __init__(self, product_id, name, category, price, stock_quantity):
        self.product_id = product_id
        self.name = name
        self.category = category
        self.price = price
        self.stock_quantity = stock_quantity

    def update_stock(self, quantity):
        self.stock_quantity += quantity

    def to_string(self):
        return f"ID: {self.product_id}, Name: {self.name}, Category: {self.category}, Price: ${self.price}, Stock: {self.stock_quantity}"


class InventoryManagementSystem:
    def __init__(self):
        self.products = {}
        self.users = {}
        self.logged_in_user = None

    def add_user(self, username, password, role):
        if username in self.users:
            print("User already exists.")
            return
        self.users[username] = User(username, password, role)

    def login(self, username, password):
        user = self.users.get(username)
        if user and user.authenticate(username, password):
            self.logged_in_user = user
            print(f"Login successful. Welcome, {username}!")
            return True
        print("Invalid credentials.")
        return False

    def logout(self):
        self.logged_in_user = None
        print("Logged out successfully.")

    def add_product(self, product_id, name, category, price, stock_quantity):
        if self.logged_in_user.role != "Admin":
            print("Unauthorized access.")
            return
        if product_id in self.products:
            print("Product ID already exists.")
            return
        self.products[product_id] = Product(product_id, name, category, price, stock_quantity)
        print("Product added successfully.")

    def edit_product(self, product_id, name=None, category=None, price=None, stock_quantity=None):
        if self.logged_in_user.role != "Admin":
            print("Unauthorized access.")
            return
        product = self.products.get(product_id)
        if not product:
            print("Product not found.")
            return
        product.name = name or product.name
        product.category = category or product.category
        product.price = price or product.price
        if stock_quantity is not None:
            product.stock_quantity = stock_quantity
        print("Product updated successfully.")

    def delete_product(self, product_id):
        if self.logged_in_user.role != "Admin":
            print("Unauthorized access.")
            return
        if product_id in self.products:
            del self.products[product_id]
            print("Product deleted successfully.")
        else:
            print("Product not found.")

    def view_inventory(self):
        if not self.products:
            print("No products in inventory.")
            return
        for product in self.products.values():
            print(product.to_string())

    def search_product(self, name=None, category=None):
        results = [product.to_string() for product in self.products.values()
                   if (name and name.lower() in product.name.lower()) or
                   (category and category.lower() in product.category.lower())]
        if results:
            for result in results:
                print(result)
        else:
            print("No products found.")

    def check_stock_levels(self, threshold=5):
        low_stock_products = [product.to_string() for product in self.products.values() if product.stock_quantity < threshold]
        if low_stock_products:
            print("Low stock alert for the following products:")
            for product in low_stock_products:
                print(product)
        else:
            print("All stock levels are sufficient.")


def main():
    ims = InventoryManagementSystem()
    ims.add_user("admin", "adminpass", "Admin")
    ims.add_user("user", "userpass", "User")

    while True:
        print("\n--- Inventory Management System ---")
        print("1. Login")
        print("2. Logout")
        print("3. Add Product (Admin only)")
        print("4. Edit Product (Admin only)")
        print("5. Delete Product (Admin only)")
        print("6. View Inventory")
        print("7. Search Product")
        print("8. Check Stock Levels")
        print("9. Change Password")
        print("10. Exit")
        choice = input("Select an option: ")

        if choice == "1":
            username = input("Username: ")
            password = input("Password: ")
            ims.login(username, password)

        elif choice == "2":
            ims.logout()

        elif choice == "3":
            if ims.logged_in_user and ims.logged_in_user.role == "Admin":
                product_id = input("Product ID: ")
                name = input("Product Name: ")
                category = input("Category: ")
                price = float(input("Price: "))
                stock_quantity = int(input("Stock Quantity: "))
                ims.add_product(product_id, name, category, price, stock_quantity)
            else:
                print("Unauthorized access. Admins only.")

        elif choice == "4":
            if ims.logged_in_user and ims.logged_in_user.role == "Admin":
                product_id = input("Product ID to edit: ")
                name = input("New Name (leave blank to skip): ")
                category = input("New Category (leave blank to skip): ")
                price = input("New Price (leave blank to skip): ")
                stock_quantity = input("New Stock Quantity (leave blank to skip): ")
                ims.edit_product(product_id, name or None, category or None, float(price) if price else None, int(stock_quantity) if stock_quantity else None)
            else:
                print("Unauthorized access. Admins only.")

        elif choice == "5":
            if ims.logged_in_user and ims.logged_in_user.role == "Admin":
                product_id = input("Product ID to delete: ")
                ims.delete_product(product_id)
            else:
                print("Unauthorized access. Admins only.")

        elif choice == "6":
            ims.view_inventory()

        elif choice == "7":
            name = input("Product name to search (leave blank to skip): ")
            category = input("Category to search (leave blank to skip): ")
            ims.search_product(name if name else None, category if category else None)

        elif choice == "8":
            ims.check_stock_levels()

        elif choice == "9":
            if ims.logged_in_user:
                old_password = input("Enter your current password: ")
                new_password = input("Enter your new password: ")
                if ims.logged_in_user.change_password(old_password, new_password):
                    print("Password changed successfully.")
                else:
                    print("Incorrect current password.")
            else:
                print("Please log in to change your password.")

        elif choice == "10":
            print("Exiting system.")
            break

        else:
            print("Invalid choice. Try again.")


if __name__ == "__main__":
    main()
