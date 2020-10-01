# Selenium_YamlTemplates

---------------  SELENIUM HUB ----------------------------

oc new-app -f templates-selenium/selenium-hub.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon"

oc delete route selenium-hub-jon -n san-altm-pre

oc delete service selenium-hub-jon -n san-altm-pre

oc delete deploymentconfig selenium-hub-jon -n san-altm-pre



----- SELENIUM NODE CHROME --------

oc new-app -f templates-selenium/selenium-node-chrome.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon" -p seleniumnodechrome="selenium-node-chrome-jon" -p replicas="2"

oc delete deploymentconfig selenium-node-chrome-jon -n san-altm-pre

oc delete service selenium-node-chrome-jon -n san-altm-pre


----- SELENIUM NODE FIREFOX --------

oc new-app -f templates-selenium/selenium-node-firefox.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon" -p seleniumnodefirefox="selenium-node-firefox-jon" -p replicas="2"

oc delete deploymentconfig selenium-node-firefox-jon -n san-altm-pre

oc delete service selenium-node-firefox-jon -n san-altm-pre



------ CREATE ----------

oc new-app -f templates-selenium/selenium-hub.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon"

oc new-app -f templates-selenium/selenium-node-chrome.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon" -p seleniumnodechrome="selenium-node-chrome-jon" -p replicas="2"

oc new-app -f templates-selenium/selenium-node-firefox.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon" -p seleniumnodefirefox="selenium-node-firefox-jon" -p replicas="2"




-----------------------  DELETE ------------------------

oc delete route selenium-hub-jon -n san-altm-pre

oc delete service selenium-hub-jon -n san-altm-pre

oc delete deploymentconfig selenium-hub-jon -n san-altm-pre

oc delete service selenium-node-chrome-jon -n san-altm-pre

oc delete service selenium-node-firefox-jon -n san-altm-pre

oc delete deploymentconfig selenium-node-chrome-jon -n san-altm-pre

oc delete deploymentconfig selenium-node-firefox-jon -n san-altm-pre




------------SI NO QUEREMOS CREAR APLICACION SI NO RESOURCES -----------------------

oc process -f templates-selenium/selenium-hub.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon" | oc create -n san-altm-pre -f - 

oc process -f templates-selenium/selenium-node-chrome.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon" -p seleniumnodechrome="selenium-node-chrome-jon" -p replicas="1" | oc create -n san-altm-pre -f -

oc process -f templates-selenium/selenium-node-firefox.yaml -n san-altm-pre -p seleniumhub="selenium-hub-jon" -p seleniumnodefirefox="selenium-node-firefox-jon" -p replicas="1" | oc create -n san-altm-pre -f -



