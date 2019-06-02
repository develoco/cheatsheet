curl, http, wget

# Download files

To get a behaviour similar to `wget` specify `output` and `silent` to suppress progress indicator.

    curl http://example.com -o -s my.file

# Examples

XML GET

    curl -H "Accept: application/xml" -H "Content-Type: application/xml" -X GET "http://hostname/resource"

JSON GET

    curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET "http://hostname/resource"

JSON PUT

    curl -i -H 'Content-Type: application/json' -H 'Accept: application/json' -X PUT -d '{"updated_field1":"updated_value1"}' "http://hostname/resourcex"

JSON POST
    curl --header "Content-Type: application/json" \
      --request POST \
      --data '{"username":"xyz","password":"xyz"}' \
      http://localhost:3000/api/login

JSON POST uploading a file

    curl -i -H 'Accept: application/json' -X POST -F "filename=@/file/path" -F "other_field=its_value"   "http://hostname/resource"

JSON DELETE

    curl -i -H 'Content-Type: application/json' -H 'Accept: application/json' -X DELETE -d '{"key1":"value1"}' "http://hostname/resource"

"Debugging mode" (without actual content output):

    curl -XGET -vvv http://hostname/resource > dev\null

# Useful arguments

    -k not check SSL certificates
    -L follow redirects
    -v get verbose output
    -V get headers at output
    
# More advanced

Read json payload from file, token from different file

    curl  -X POST \
      -H "Content-Type: application/json" \
      -H "Authorization: Bearer `cat last_token`" \
      -d @payload/newproject.json \
      "http://localhost:3000/v1/projects"

Pipe json result to node script for processing

    curl  -X POST \
      -H 'Content-Type: application/json' \
      -d @payload/login.json \
      "http://localhost:3000/v1/users/sign_in" | ./savetoken.js > last_token
