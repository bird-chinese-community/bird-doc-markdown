## Operators

The filter language supports common integer operators `(+,-,*,/)`,
parentheses `(a*(b+c))`, comparison `(a=b, a!=b, a<b, a>=b)`.

Logical operations include unary not (`!`), and (`&&`), and or
(`||`).

Strings and bytestrings can be concatenated with `++` operator.

Special operators include (`~`, `!~`) for "is (not) element
of a set" operation - it can be used on:

- element and set of elements of the same type (returning true if
      element is contained in the given set)
- two strings (returning true if the first string matches a shell-like
      pattern stored in the second string)
- IP and prefix (returning true if IP is within the range defined by
      that prefix)
- prefix and prefix (returning true if the first prefix is more specific
      than the second one)
- bgppath and bgpmask (returning true if the path matches the mask)
- number and bgppath (returning true if the number is in the path)
- bgppath and int (number) set (returning true if any ASN from the
      path is in the set)
- pair/quad and clist (returning true if the pair/quad is element of
      the clist)
- clist and pair/quad set (returning true if there is an element of the
      clist that is also a member of the pair/quad set).

There are also operators related to RPKI infrastructure used to run
<a href="https://datatracker.ietf.org/doc/rfc6483" class="rfc">RFC
6483</a> route origin validation and (draft) AS path validation.

- `roa_check(`
  *table*
  `)`
   checks the current route in the specified
    ROA table and returns 
  `ROA_UNKNOWN`
  , 
  `ROA_INVALID`
   or 
  `ROA_VALID`
  ,
    if the validation result is unknown, invalid, or valid, respectively. The result is
    valid if there is a matching ROA, it is invalid if there is either matching ROA
    with a different ASN, or any covering ROA with shorter maximal prefix length.
- `roa_check(`
  *table*
  `, `
  *prefix*
  `, `
  *asn*
  `)`
   is an explicit version
    of the ROA check if the user for whatever reason needs to check a different prefix
    or different ASN than the default one. The equivalent call of the short variant
    is 
  `roa_check(`
  *table*
  `, net, bgp_path.last)`
   and it is faster
    to call the short variant.
- `aspa_check_downstream(`
  *table*
  `)`
   checks the current route
    in the specified ASPA table and returns 
  `ASPA_UNKNOWN`
  , 
  `ASPA_INVALID`
  ,
    or 
  `ASPA_VALID`
   if the validation result is unknown, invalid, or valid,
    respectively. The result is valid if there is a full coverage of matching
    ASPA records according to the Algorithm for Downstream Paths by the (draft).
    This operator is not present if BGP is not compiled in.
- `aspa_check_upstream(`
  *table*
  `)`
   checks the current route
    in the specified ASPA table as the former operator, but it applies the
    (stricter) Algorithm for Upstream Paths by the (draft).
    This operator is not present if BGP is not compiled in.
- `aspa_check(`
  *table*
  `, `
  *path*
  `, `
  *is_upstream*
  `)`
   is
    an explicit version of the former two ASPA check operators. The equivalent
    of 
  `aspa_check_downstream`
   is 
  `aspa_check(`
  *table*
  `, bgp_path, false)`

    and for 
  `aspa_check_upstream`
   it is
    
  `aspa_check(`
  *table*
  `, bgp_path, true)`
  .
    Note: the ASPA check does not include the local ASN in the AS path.
    Also, 
  `ASPA_INVALID`
   is returned for an empty AS path
    or for AS path containing 
  `CONFED_SET`
   or 
  `CONFED_SEQUENCE`
   blocks,
    as the (draft) stipulates.

The following example checks for ROA and ASPA on routes from a customer:


    roa6 table r6;
    aspa table at;
    attribute int valid_roa;
    attribute int valid_aspa;

    filter customer_check {
      case roa_check(r6) {
        ROA_INVALID: reject "Invalid ROA";
        ROA_VALID: valid_roa = 1;
      }

      case aspa_check_upstream(at) {
        ASPA_INVALID: reject "Invalid ASPA";
        ASPA_VALID: valid_aspa = 1;
      }

      accept;
    }
