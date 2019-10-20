#include <stdio.h>
#include <string.h>
#include <stdlib.h>

/* Begin modelling the entities: Shop, Customer, Products, Stock */

// Product entity:
struct Product{
	char* name;
	double price;
	
};

// Stock entity:
// i.e. products available in the shop
/* This below will provide a LINK between the stock and the quantity that we have of the stock*/
struct ProductStock{ 
	struct Product product; // the name
	int quantity;			// the amount in stock
	
};

// Shop entity:
struct Shop{
	double cashfloat;				// Shop's float ("cash" in Dominics)
	struct ProductStock stock[20]; 	// ProductStock is how many of each product we have
	int index; // To keep track of in/outs of stock
	
};

// Customer entity:
struct Customer{
	char* name; // pointer* allows memory expansion
	double budget;
	struct ProductStock shoppingList[10]; /*Customer shopping list*/
	int index; // keeps track of the value
	
};

/* End of modelling the entities */

/*------------------------------------------------*/

/* Method to print out the product and customer information 
e.g. passing the struct into a method and using it etc*/

// Product info:
void printProduct(struct Product p) // takes in a product as argument
{
	
	printf("PRODUCT: NAME: %s \nPRODUCT PRICE: %.2f\n", p.name, p.price); // p was formerly "coke"
	printf("------------------\n");
}	// Will print product name on one line and the price on the next line

// Customer info:
void printCustomer(struct Customer c)
{
	
	printf("CUSTOMER: NAME: %s \nCUSTOMER BUDGET: %.2f\n", c.name, c.budget); // c was formerly "Gareth": remember, c is customer's name
	printf("------------------\n");
	// for loop to loop through the desired customer products needed below
	for(int i = 0; i < c.index; i++)
	{
		printProduct(c.shoppingList[i].product); // passing the customers shopping list!
		printf("%s ORDERS %d OF ABOVE PRODUCT\n", c.name, c.shoppingList[i].quantity);
		// above line is chaining of access for the order(how much they want:quantity)
		// below line is the cost calculator algorithm (looks inside variables to retrieve)
		double cost = c.shoppingList[i].quantity * c.shoppingList[i].product.price;
		printf("The cost to %s will be €%.2f\n", c.name, cost);
	}
}

// Method to read in the stock file/customer lists etc:
struct Shop createAndStockShop()
{ 	// struct that represents the shop:
	struct Shop shop = {200};
	// reading in from the file:
    FILE * fp;
    char * line = NULL;
    size_t len = 0;
    ssize_t read;

    fp = fopen("stock.csv", "r"); // r means read w form write etc
    if (fp == NULL)
        exit(EXIT_FAILURE);

    while ((read = getline(&line, &len, fp)) != -1) {
        // printf("Retrieved line of length %zu:\n", read);
        // printf("%s IS A LINE", line);
		// Below is the string tokeniser at work:
		char *n = strtok(line, ",");
		char *p = strtok(NULL, ","); // NULL doean't take an input
		char *q = strtok(NULL, ",");
		int quantity = atoi(q);
		double price = atof(p);
		char *name = malloc(sizeof(char) * 50);
		strcpy(name, n);
		struct Product product = {name, price};
		struct ProductStock stockItem = {product, quantity};
		shop.stock[shop.index++] = stockItem;
		
		// printf("NAME of product %s PRICE %.2f QUANTITY %d\n", name, price, quantity);
		//printf("NAME OF PRODUCT %s\n", name);
    }
	// printProduct(shop.stock[0].product);
	return shop;
	
}

// creating a print method for the shop ITSELF:

void printShop(struct Shop s)
{
	printf("Shop has %.2f in cash\n", s.cashfloat);
	// middle part of a for loop is the condition
	for (int i = 0; i < s.index; i++)
	{
		printProduct(s.stock[i].product);
		printf("The shop has %d of the above (QUANTITY)\n", s.stock[i].quantity);
		
	}
}

int main(void)
{
	//ASSOCIATED STRUCTS:
	
	//struct Customer gareth = {"Gareth",100.0};	
		
	//struct Product coke = {"Can Coke", 1.10};
	//struct Product bread = {"Bread", 0.70};
	// printProduct(coke); // Calling the method (coke)
	
	// Below shows the quantity the customer wants:	
	//struct ProductStock cokeStock = {coke, 20};
	//struct ProductStock breadStock = {bread, 2};
	
	//gareth.shoppingList[gareth.index++]= cokeStock; // [0] in the array/retrieves the index value (This adds an item to the customers order)
	//gareth.shoppingList[gareth.index++]= breadStock;
	
	//printCustomer(gareth); // Calling the method
	// printf("The shop has %d of the product %s\n", cokeStock.quantity, cokeStock.product.name); /* Pulling data out of parent data */
	
	struct Shop shop = createAndStockShop();
	printShop(shop);
	
	
	return 0;
}