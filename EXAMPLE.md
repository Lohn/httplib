# Example Bash CGI script

```
#!/bin/bash
#
# Caveat: Remember the empty line after echoing headers
#

# httplib creates the associative arrays POST_PARAMS and GET_PARAMS
if [[ "$SCRIPT_FILENAME" ]]; then
  . "$(dirname $SCRIPT_FILENAME)/httplib"
else
  . "$(dirname $(pwd)$SCRIPT_NAME)/httplib"
fi

POST_vars_to_str() {
  local __resultvar=$1
  local q
  for param in "${!POST_PARAMS[@]}"; do
    q="${q} \"${param}\": \"${POST_PARAMS[$param]}\"," 
  done
  eval $__resultvar="'$q'"
}

do_POST() {
  POST_vars_to_str result
  echo "Status: 200 OK"
  echo ""
  cat <<JSON
{
    $result
}
JSON
}
do_GET() {
  echo "Status: 405 Method Not Allowed"
  echo ""
  cat <<JSON
{
    "query_string": "$QUERY_STRING"
}
JSON
}

# Print out available ENV vars
/usr/bin/env

# Common headers goes here
echo "Content-Type: application/json"

case $REQUEST_METHOD in
  POST)
    do_POST
    ;;
  GET)
    do_GET
    ;;
  *)
    echo "No handle for $REQUEST_METHOD"
    exit 0
    ;;
esac
```