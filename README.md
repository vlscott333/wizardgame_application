# wizardgame_application
'''This program should read the text file named wizard_all_items.txt
Download wizard_all_items.txt that contains all the items a wizard can carry.

When the user selects the walk command, the program should randomly pick one
of the items that were read from the text file and give the user the option to grab it.

The current items that the wizard is carrying should be saved in an inventory file.
Make sure to update this file every time the user grabs or drops an item.

The wizard can only carry four items at a time. For the drop command, display
an error message if the user enters an invalid integer or an integer that doesn’t
correspond with an item.

Handle all exceptions that might occur so that the user
can’t cause the program to crash. If the all items file is missing, display an appropriate
error message and exit the program as shown in the console output below

If the inventory file is missing, display an appropriate error message as shown in the
console output below and continue with an empty inventory for the user. That way,
the program will write a new inventory file when the user adds items to the inventory.'''

import os
import random
from pathlib import Path

base_directory = os.getcwd()
ITEMS_FILENAME = os.path.join(base_directory, "wizard_all_items.txt")
INVENTORY_FILENAME = os.path.join(base_directory, "wizard_inventory.txt")

def read_items():
    items = []
    try:
        with open(ITEMS_FILENAME) as file:
            for line in file:
                line = line.replace("\n", "")
                items.append(line)
        return items
    except FileNotFoundError:
        print('File for Wizard Game not found \n'
              '***CLOSING APPLICATION***')

def read_inventory():
    inventory = []
    try:
        if os.stat(INVENTORY_FILENAME).st_size > 0:
            with open(INVENTORY_FILENAME) as file:
                for line in file:
                    line = line.replace("\n", "")
                    inventory.append(line)
                else:
                    print ('The wizard is starting with no items')
    except FileNotFoundError:
        print('The wizard starts with no items, select walk to find items')
        Path(INVENTORY_FILENAME).touch()
    finally:
        return inventory

def write_inventory(inventory):
    with open(INVENTORY_FILENAME, "w") as file:
        for item in inventory:
            file.write(item + "\n")

def display_title():
    print("The Wizard Inventory program")
    print()

def display_menu():
    print("COMMAND MENU")
    print("walk - Walk down the path")
    print("show - Show all items")
    print("drop - Drop an item")
    print("exit - Exit program")
    print()

def walk(inventory):
    items = read_items()
    item = random.choice(items)
    print("While walking down a path, you see " + item + ".")
    choice = input("Do you want to grab it? (y/n): ")
    if choice == "y":
        if len(inventory) >= 4:
            print("You can't carry any more items. " +
                  "Drop something first.\n")
        else:
            inventory.append(item)
            print("You picked up " + item + ".\n")
            write_inventory(inventory)

def show(inventory):
    if len(inventory) == 0:
        print('No items in your inventory yet')
    else:
        for i in range(len(inventory)):
            item = inventory[i]
            number = i + 1
            print(str(number) + ". " + item)
        print()

def drop_item(inventory):
    number = int(input("Number: "))
    if number < 1 or number > len(inventory):
        print("Invalid item number.\n")
    else:
        item = inventory.pop(number - 1)
        print("You dropped " + item + ".\n")
        write_inventory(inventory)

def close_app(inventory):
    '''Will delete ALL items in inventory bank when exiting application'''
    try:
        if inventory != 0:
            os.remove(INVENTORY_FILENAME)
            close_app(inventory)
        else:
            pass
    except FileNotFoundError:
        print("The application has closed and reset your \ninventory bank back to 0, re-run to play again")

def main():
    display_title()
    display_menu()

    inventory = read_inventory()
    while True:
        command = input("Command: ")
        if command == "walk":
            walk(inventory)
        elif command == "show":
            show(inventory)
        elif command == "drop":
            drop_item(inventory)
        elif command == "exit":
            close_app(inventory)
            break
        else:
            print("Not a valid command. Please try again.\n")
    print("Bye!")


if __name__ == "__main__":
    main()
