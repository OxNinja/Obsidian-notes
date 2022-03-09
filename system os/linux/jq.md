#linux #json #cli
The [[Linux]] CLI [[JSON]] parser
`curl ... | jq`
`| jq '.[].name'`
`| jq '.[] | select (.type == "bla")' | .name ` get `name` of objects with type `bla`

