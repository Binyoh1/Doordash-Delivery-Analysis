# DoorDash Delivery Data Exploration
## by Binyoh Langhe Theodore


## About the Data
This dataset contains a subset of deliveries received at DoorDash in 2015 in a subset of the cities. The dataset is available on [Stratascratch](https://platform.stratascratch.com/data-projects/delivery-duration-prediction). Each row in this dataset corresponds to one unique delivery, and each column corresponds to a feature as explained below.

**Note**: All money (dollar) values given in the data are in `cents` and all time duration values given are in `seconds`.

Download and open the **Part_II_slide_deck_template.slides.html** to view the the slide presentation for this project.
You can view the presentation online using this [link](https://binyoh1.github.io/Doordash-Delivery-Analysis/#/)



## Data Dictionary
### Time Features
- market_id: A city/region in which DoorDash operates, e.g., Los Angeles, given in the data as an id.
- created_at: Timestamp in UTC when the order was submitted by the consumer to DoorDash.
- actual_delivery_time: Timestamp in UTC when the order was delivered to the consumer.

### Store Features
- store_id: an id representing the restaurant the order was submitted for.
- store_primary_category: cuisine category of the restaurant, e.g., italian, asian.
- order_protocol: a store can receive orders from DoorDash through many modes. This field represents an id denoting the protocol.

### Order Features
- total_items: total number of items in the order.
- subtotal: total value of the order submitted (in cents).
- num_distinct_items: number of distinct items included in the order.
- min_item_price: price of the item with the least cost in the order (in cents).
- max_item_price: price of the item with the highest cost in the order (in cents).

### Market Features
- total_onshift_dashers: Number of available dashers who are within 10 miles of the store at the time of order creation
- total_busy_dashers: Subset of above total_onshift_dashers who are currently working on an order
- total_outstanding_orders: Number of orders within 10 miles of this order that are currently being processed.

### Predictions from Machine Learning Models
- estimated_order_place_duration: Estimated time for the restaurant to receive the order from DoorDash (in seconds).
- estimated_store_to_consumer_driving_duration: Estimated travel time between store and consumer (in seconds).


## Cleaning Issues - Logical Errors
Just looking at the summary statistics of the data, some glaring logical and numerical/statistical issues in some columns have been indentified:
- The lowest subtotal value is zero, which implies there is at least one order amounting to 0 cents.
- Minimum item price has at least one negative value, since the lowest value is -86.
- Maximum item price has zero as the lowest value, implying some orders may have zero sum cost.
- The lowest value for total onshift dashers is -4, implying there is at least one negative entry for this field.
- Similarly, total busy dashers has at least one negative entry, since the lowest value in the column is -5.
- Total outstanding orders also has at least one negative entry, since the lowest value in the column is -6.

Given these issues, it'll be worth looking into orders wherein:
- the subtotal value is less than the maximum item price or subtotal value is less than the sum of maximum and minimum item prices for orders with more than one item (this should be impossible since the subtotal is the sum of the costs of each item in the order).
- the minimum item price is greater than the maximum item price.
- the total number of items is less than the number of distinct items.
- the actual delivery time is less than the time when the order was made (created_at column)

**Note**: It'll also be worth looking into the number of orders with minimum item price values equal to zero. This situation logically makes sense as some orders may have special discounts or coupons. However if the number of such orders is significant (say over 5-6% of all orders), then it could be a call for concern regarding profit margins.


## Project Goal
The aim of this analytical exploration is to find out what factors affect delivery duration, and hence could be useful in predicting the delivery duration for any order. As such, I intend to answer the following questions:
- What features or variables affact delivery duration?
    - How do they relate to delivery duration and each other?
- Which markets/regions and/or protocols if any have a large proportion of orders with high delivery duration values?
- What can be done to reduce the frequency of orders with high delivery duration values? 


## Summary of Findings

> Prior to analysis, I expected that the estimated order creation to delivery duration, the availability of free dashers, ratio of free dashers to onshift dashers (free_dashers_ratio), and subtotal would have the strongest effect on the delivery duration. Total outstanding orders as well might affect the delivery duration to some extent.

The analysis indicate that delivery duration is likely affected by:
- the estimated order creation to delivery duration
- the availability of free dashers within 10 miles of the corresponding store
- the proportion of onshift dashers within a 10 mile radius of the store who aren't working on another order
- the number of orders within 10 miles of the store being processed.

Markets 1, and 3 likely require the recruitment of dashers to reduce the frequency of orders where not dashers are available within a 10 mile radius of the store to deliver the order. Similarly, protocol 6 orders would also require more dashers to reduce the frequency of orders without free dashers available.

Almost 20.5% of all records in the original dataset had values of total onshift dashers less than those of total busy dashers. More care should be taken when recording dasher count values to avoid the frequency of such errors. In this, case I simply swapped the values between these 2 columns where this error occurred.
