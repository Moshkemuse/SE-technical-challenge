SE-technical-challenge resolution runbook
--by Moises Bautista--


1- You received a message from a person from the Go To Market team that oversee the Stores operation. They need an information from our database and you could help this person to have it by doing a query there. The person requested a list of all the tags that we currently use and how many stores are using each tag. Create a query that allow you to provide this information to the Go To Market team.

 	- select * from stores; --query to see the table structure, finding two columns: id(text), data(json)
	- Within data(json) there's a key called "tags", it is a json as well, containing usually 3 tags. e.g: ""tags"": {""tag1"": ""..."", ""tag2"": ""..."", 	         ""tag3"": ""...""}
	- select * from stores s, json_each_text(s.data->'tags'); --using the json_each_text function to see how the tags json is pulled, finding it brings two new          columns: key, value.
	- select t.value as tags, count(*) as stores_count from stores s, json_each_text(s.data->'tags') as t group by t.value; --final sentence to group by the 		  different tags with the stores count.
	- Showing this result:
	tags            stores_count
	"finanzas"		106
	"tecnologia"	98
	"movilidad"		95
	"hogar"	        99
	"belleza"		105
	"educacion"		93
	"desportes"		104
	"apparel"		94
	"ropa"	        106

2- We just received a case where the owner of the store with the id 0d4f7a40-651d-44fb-8744-04d9b31ef844 changed to Old-Wolf. They also stopped to sell things related to finanzas and are now working with educacion so they also required a change in their current tags. Since this is urgent we will do that thru a query. Create a query that allow us to do this update in our database.

	- select data from stores where id = '0d4f7a40-651d-44fb-8744-04d9b31ef844'; --query to check current values of that customer, with its id.
	- update stores set data = jsonb_set(data::jsonb, '{name}', '"Old-Wolf"') where id = '0d4f7a40-651d-44fb-8744-04d9b31ef844'; --using the jsonb_set function to 	  update the 'name', and then seeting the new value as Old-Wolf, using the customer id.
	- update stores set data = jsonb_set(data::jsonb, '{tags,tag1}', '"educacion"') where id = '0d4f7a40-651d-44fb-8744-04d9b31ef844'; --updating as nested json 	  for tags. Both keys go in the same Brackets, first "tags" and after a comma the inner key to be change, in this case it's "tag1".
	- select data from stores where id = '0d4f7a40-651d-44fb-8744-04d9b31ef844'; --query to confirm the changes.

3- Since we are having a hard time while creating all the store credentials manually thru insert queries we recently received a message from one Software Engineer that build an API that will allow you to create new stores quickly. Currently we don't have system integrated thru this API but we can use it right now. Build an HTTP request to add the store credentials through our API. The id of the store is 1f585fc9-4926-468c-a728-eb34d80e9ea1

	- Using Postman (or any API requests language).
	- Use and send the method GET with the URL: http://127.0.0.1:4000/allies/1f585fc9-4926-468c-a728-eb34d80e9ea1/credentials --to check the current values. 	 	  "message": "Ally does not have credentials set!"
	- Use the method POST with URL: http://127.0.0.1:4000/allies/1f585fc9-4926-468c-a728-eb34d80e9ea1/credentials
	- Setting up the params = allyId : 1f585fc9-4926-468c-a728-eb34d80e9ea1.
	- Headers = Content-Type: application/json
	- For the body, selecting "raw" option: {
	                                            "username": "aliado_addi",
   	 					    					"password": "}sxh7_5}BdJ4K:Qf"
											}
	- After clicking on send it gets the message: {
 			 	 	 		  							"AllyId": "1f585fc9-4926-468c-a728-eb34d80e9ea1",
    							 						"AllyName": "Barnes Inc",
    							  						"message": "Credentials added"
						     					  }
	- Use and send again the method GET with the URL: http://127.0.0.1:4000/allies/1f585fc9-4926-468c-a728-eb34d80e9ea1/credentials --seeing "message": "Ally has 	  credentials set!"
	OPTIONAL: select data->>'credentials' as credentials from stores where id = '1f585fc9-4926-468c-a728-eb34d80e9ea1'; --to verify in the database. The 			credentials value is encrypted. Otherwise would be null.

4- After some time people started to complain that their credentials are not working. Probably something happened while other Support Engineers were also using this API. You have access to the server log that have all the previous requests. You should open the log inside the folder log/example.log and find how many requests failed and which are the clients that were impacted.

	- cd to the log folder. e.g: /home/user/gitHub/SE-technical-challenge/extra/log
	- To load thefailed requests, run: grep -E '\b(4|5)[0-9]{2}\b' example.log
	- To count 400 and 500 results, run: grep -c '\b40[0-9]\b' example.log --counting 177. Then grep -c '\b50[0-9]\b' example.log --counting 95.
	- To print the affected customers list, run: grep -E '\b(4|5)[0-9]{2}\b' example.log   | awk '{print $7}'   | sort   | uniq -c   | sort -nr
	  	-- Using the original regex to unify 400 and 500 responses.
		-- awk '{print $7}': as the customers is always in the seventh field.
		-- sort   | uniq -c   | sort -nr: sorting by unique result and descendent counting
		-- getting the result below:
			33 /ex/voluptatibus/praesentium
	        30 /maxime/placeat/fuga
            29 /aspernatur
	        27 /doloremque/quae
	        27 /consequuntur/rerum/veniam
	        26 /magni/alias
	        26 /iure/delectus
	        26 /dignissimos/repellat
	        24 /quibusdam/enim/ipsam
	        21 /aliquid/numquam
	         3 "POST
Note: find each item's solution attached in the scripts folder.
--END--
