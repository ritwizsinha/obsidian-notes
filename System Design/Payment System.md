1. Fault Tolerance and reliability is necessary because it interacts with a bunch of components and might become incosistent.
2. Asynchronous Reconcilation between systems is important.
3. Keeping a Ledger or audit of payments is necessary in payment applications.
4. While performing Pay-In we can maintain a seller side wallet of the total that a seller has.
5. While executing a third party payment the client needs to provide an idempotency key associated with a payment to signify that the transaction was already submitted.
6. Avoid storing numbers in double because of precision issues, string is best.
7. Double entry ledger. Always store records for both credit and debit thus the whole summation is zero.
8. Where should the idempotency key be generated. Ideally it should be generated or be the unique session id so that the key is same even if the user clicks pay multiple times.
9. For unsuccessful transactions we can use the retry and dead letter queue.
	1. If multiple concurrent requests are detected with the same idempotency key only one is processed and others are given 429 