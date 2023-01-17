Learn how to use a Bloom filter to reduce heavy calls to the relational database or memory compared to using sets or hashes.

A Bloom filter is a probabilistic data structure that enables you to check if an element is present in a set using a very small memory space of a fixed size. It can guarantee the absence of an element from a set, but it can only give an estimation about its presence. So, when it responds that an element is not present in a set (a negative answer), you can be sure that indeed is the case. However, one out of every N positive answers will be wrong.

Even though it looks unusual at a first glance, a negative answer prevents costly operations.

How can a Bloom filter be used for our bike shop? For starters, you could keep a Bloom filter that stores all usernames of people who've already registered with our service. That way, when someone creates a new account, you can very quickly check if that username is free. If the answer is yes, you still have to go and check the main database for the precise result, but if the answer is no, you can skip that call and continue with the registration. 

Another, perhaps more interesting example, is showing better and more relevant ads to users. You can keep a Bloom filter per user with all the products they bought from the shop, and when you get a list of products from your suggestion engine, you can check it against this filter.

```redis Add all bought product ids in the Bloom filter
BF.MADD user:778:bought_products  4545667 9026875 3178945 4848754 1242449
```

Just before you try to show an ad to a user, you can first check if that product id is already in their _bought products_ Bloom filter. If the answer is yes, you can choose to check the main database, or you might skip to the next recommendation from your list. But if the answer is no, then you know for sure that your user did not buy that product:

```redis Has a user bought this product?
BF.EXISTS  user:778:bought_products 1234567  // No, the user has not bought this product
BF.EXISTS  user:778:bought_products 3178945  // The user might have bought this product
```
