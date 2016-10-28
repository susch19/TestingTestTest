```C#
public static void NAMEDERMETHODE(
    SAHECustomerId _customerId,
    SAHEInvoiceId _invoiceId,
    date _invoiceDate)
{
    SAHERentalTable         rentalTable;
    SAHEInvoiceTable        invoiceTable;
    SAHEInvoiceLineTable    invoiceLineTable;
    int                     i = 0;
    real                    price;
    utcDateTime             dtime = DateTimeUtil::newDateTime(_invoiceDate, DateTimeUtil::time(DateTimeUtil::utcNow()));

    while select forUpdate rentalTable
        where rentalTable.CustomerId    == _customerId
            && rentalTable.Invoiced     == NoYes::No
            && rentalTable.RentalEnd    <  dtime
            && rentalTable.RentalEnd    != str2datetime("", 0)
    {
        i++;
        rentalTable.Invoiced = true;
        invoiceLineTable.ChassisId          = rentalTable.ChassisId;
        invoiceLineTable.Discount           = rentalTable.Discount;
        invoiceLineTable.InvoiceId          = _invoiceId;
        invoiceLineTable.ReferencedRental   = rentalTable.RecId;
        invoiceLineTable.RentalFrom         = rentalTable.RentalFrom;
        invoiceLineTable.RentalPerDay       = rentalTable.RentalPerDay;
        invoiceLineTable.RentalTo           = rentalTable.RentalEnd;
        invoiceLineTable.LinePrice          = rentalTable.calcPrice();
        invoiceLineTable.LineNumber         = i;
        price                              += invoiceLineTable.LinePrice;
        if (invoiceLineTable.validateWrite()
            && rentalTable.validateWrite())
        {

            ttsBegin;
            invoiceLineTable.insert();
            rentalTable.update();
            ttsCommit;
        }

    }

    invoiceTable.InvoicePrice   = price;
    invoiceTable.InvoiceDate    = DateTimeUtil::date(dtime);
    invoiceTable.BranchId       = rentalTable.BranchId;
    invoiceTable.InvoiceId      = _invoiceId;
    invoiceTable.CustomerId     = _customerId;

    if (invoiceTable.validateWrite())
    {
        invoiceTable.insert();
    }
    public display SAHEInvoiceDate getDate()
{
    return SAHEInvoiceTable::find(this.InvoiceId).IvoiceDate;
}
}
```

Headline h2
-----------  
#Headline
###Another one
##Second
####Even four  
[markdown]: https://daringfireball.net/projects/markdown/syntax "Syntax für ihr Markdown"
##### Is the fifth even a headline

normal text

###### h6 is the limit  
***both***

2. this (2)  
5. list (5)  
1. is (1)  
4. unsorted (4)  
3. in (3)
1. the (1)  
2. code (2)

4\. No List anymore  
3\. third

* Kein Unterpunkt 
    * Unterpunkt [google]
* Back to normal [markdown]
    * aklsjd  
        * aslkjd  
    * kqwje

[Ein Link zum drauf drücken](https://www.google.de)

<email@adress.de>
[google]: https://google.com/  "Kinda Like a helptext"
[google2]: https://google.com/  (Kinda Like a helptext)
[google3]: https://google.com/  'Kinda Like a helptext'
[google4]: <https://google.com/>  "Kinda Like a helptext"
