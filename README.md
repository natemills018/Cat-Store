# Cat-Store


DROP SCHEMA IF EXISTS sp_cat_lab;
CREATE SCHEMA sp_cat_lab;
USE sp_cat_lab;

CREATE TABLE customers (
    id INT AUTO_INCREMENT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    PRIMARY KEY (id)
);

CREATE TABLE orders (
    id INT AUTO_INCREMENT,
    customer_id INT,
    product_name VARCHAR(100),
    quantity INT,
    PRIMARY KEY (id),
    FOREIGN KEY (customer_id) REFERENCES customers (id)
);

INSERT INTO customers (first_name, last_name, email)
VALUES
('Whiskers', 'McMeow', 'whiskers.mcmeow@catmail.com'),
('Fluffy', 'Purrington', 'fluffy.purrington@catmail.com'),
('Mittens', 'Clawson', 'mittens.clawson@catmail.com'),
('Shadow', 'Hissster', 'shadow.hissster@catmail.com'),
('Luna', 'Tailsworth', 'luna.tailsworth@catmail.com');

INSERT INTO orders (customer_id, product_name, quantity)
VALUES
(1, 'Catnip Toy', 2),
(1, 'Scratching Post', 1),
(1, 'Fish-shaped Food Bowl', 1),
(2, 'Catnip Toy', 3),
(2, 'Soft Bed', 1),
(2, 'Mouse Plush', 3),
(3, 'Mouse Plush', 4),
(3, 'Cat Collar with Bell', 1),
(3, 'Tuna Treats Pack', 2),
(4, 'Laser Pointer Toy', 1),
(4, 'Soft Bed', 2),
(4, 'Kitty Litter Scooper', 1),
(5, 'Sisal Ball', 3),
(5, 'Mouse Plush', 2),
(5, 'Catnip Pouch', 2);


DELIMITER $$
DROP PROCEDURE IF EXISTS get_customer_orders $$
CREATE PROCEDURE get_customer_orders( IN customer_id INT)
BEGIN
	SELECT 
    customers.id,
    customers.first_name,
    orders.product_name,
    orders.quantity
    FROM 
    orders 
    JOIN customers ON customers.id = orders.customer_id;
END $$
DELIMITER ;

CALL get_customer_orders();


DELIMITER $$
DROP PROCEDURE IF EXISTS add_new_order $$
CREATE PROCEDURE add_new_order( IN c_id INT, IN _prod_name VARCHAR(100), IN _quant INT)
BEGIN
	INSERT INTO orders (customer_id, product_name, quantity)
    VALUE (c_id, _prod_name, _quant);
    
END $$
DELIMITER ;


CALL add_new_order ( '3', 'Bananas', '30');

DELIMITER $$
DROP PROCEDURE IF EXISTS update_order_quantity $$
CREATE PROCEDURE update_order_quantity( IN _orders_id INT, IN new_quant INT)
BEGIN
	UPDATE orders
    SET quantity = new_quant
    WHERE customer_id = _orders_id;
END $$
DELIMITER ;



CALL update_order_quantity(4,7);
 -- Here I'm selecting the specific customer id, and raising the quantities of all of the specific products that they are associated with.

DELIMITER $$
DROP PROCEDURE IF EXISTS delete_order $$
CREATE PROCEDURE delete_order( IN _orders_id INT)
BEGIN
	DELETE FROM orders
    WHERE orders.id = _orders_id;
END $$
DELIMITER ;

CALL delete_order(2);

DELIMITER $$
DROP PROCEDURE IF EXISTS find_customers_by_product $$
CREATE PROCEDURE find_customers_by_product( IN _prod_name VARCHAR(100))
BEGIN
	SELECT o.customer_id, o.product_name, o.quantity
    FROM orders o
	WHERE o.product_name = _prod_name;
END $$
DELIMITER ;

CALL find_customers_by_product('Mouse Plush');


SELECT * FROM orders;
