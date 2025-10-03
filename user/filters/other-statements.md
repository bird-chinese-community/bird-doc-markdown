## Other statements

The following statements are available:

<span id="assignment" class="code">*variable* = *expr*</span>  
Set variable (or route attribute) to a given value.

<span id="filter-accept-reject" class="code">accept\|reject \[ *expr* \]</span>  
Accept or reject the route, possibly printing *expr*.

<span id="return" class="code">return *expr*</span>  
Return *expr* from the current function, the function ends
    at this point.

<span id="print" class="code">print\|printn *expr* \[*, expr...*\]</span>  
Prints given expressions; useful mainly while debugging filters. The
    `printn` variant does not terminate the line.
