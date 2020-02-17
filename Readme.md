https://www.hackster.io/jsmsolns/arduino-toyota-uk-tpms-tyre-pressure-display-b6e544

https://www.hackster.io/jsmsolns/arduino-renault-tpms-tyre-pressure-display-dfb548

Incomming Timings can also be put into Wolfgang's decoder.

Incomming "raw bits" (still Manchester encoded) can be searched for the end of the preamble (AA, A9 => 10101010 10101001).
Everything after than can be put through https://eleif.net/manchester.html to get the actual bits.

The bits can be put into the Google table "433" (link withheld).