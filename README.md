# IPLookupApp
IPLookup application from the ServiceNow Outbound REST Integrations module

Platform: ServiceNow
Instance URL:https://dev268218.service-now.com/
Web Service : https://ipinfo.io/

Description:
1. Created an application in Servicenow Studio named NewIPLookUp.
2. Created a Table named NewIPAddressInfos to be visible and where CRUD operations can be perfomed in the Servicenow Instance under the scoped application NewIPLookUp.
3. In the table, fields added as follows: IP Address (String) , City (String) , Country (String) , Hostname (String) , Region (String) , Postal (String) , Time Zone (String)
4. Organised the Table layout in a way that the IP Address is on the left side and other fields are in the right side so that when the IP address is entered, the relevent fields will get automatically filled up.
5. Created a REST Message application file to fetch data in JSON format when my Servicenow instance request for IP information data from https://ipinfo.io/
   * HTTP Method with GETIPInfo named created by default
   * HTTP Method: GET (used to fetch data)
   * Endpoint: https://ipinfo.io/${ipaddress}/json
   * Auto-generated the variable ipaddress and provided a testing value : 8.8.8.8 with Escape Type : No Escaping
   * Tested and received 200 status code meaning request succeded.
   * Response : {
  "ip": "8.8.8.8",
  "hostname": "dns.google",
  "city": "Mountain View",
  "region": "California",
  "country": "US",
  "loc": "37.4056,-122.0775",
  "org": "AS15169 Google LLC",
  "postal": "94043",
  "timezone": "America/Los_Angeles",
  "readme": "https://ipinfo.io/missingauth",
  "anycast": true
}

6. Created a Asynchronous Business Rule for Insertion & Updation only to Populated IP Address City field and tested. Lated on success added other fields in the script to be filled simulataneouly:
   *SOURCE CODE*
   (function executeRule(current, previous /*null when async*/) {
 try { 
 var r = new sn_ws.RESTMessageV2('x_1777657_newiploo.NewIPLookUP', 'GETIPInfo');
 r.setStringParameterNoEscape('ipaddress', current.ip_address);

 var response = r.execute();
 var responseBody = response.getBody();
 var httpStatus = response.getStatusCode();

 var responseObj = JSON.parse(responseBody);
 current.city = responseObj.city;
 gs.info("City = " + current.city);
 current.country = responseObj.country;
 gs.info("Country = " + current.country);
 current.hostname = responseObj.hostname;
 gs.info("Hostname =" + current.hostname);
 current.region = responseObj.region;
 gs.info("Region =" + current.region);
 current.postal = responseObj.postal;
 gs.info("Postal =" + current.postal);
 current.time_zone = responseObj.timezone;
 gs.info("Time Zone =" + current.time_zone);
 current.update();
}
catch(ex) {
 var message = ex.message;
}

})(current, previous);

TESTING
---------

In ServiceNow, went to NewIPAddressInfos table and inserted an IP address in the IP Address field and saved, the relevent fields get filled up automatically.


   
