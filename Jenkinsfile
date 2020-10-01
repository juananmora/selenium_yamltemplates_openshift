#!/usr/bin/groovy

@Library('jenkins-shared-utils@0.0.1')

import java.util.HashMap;

private static String OPENSHIFT_URL_PRO1 = "https://api.san01.san.pre.bo1.paas.cloudcenter.corp:8443";
private static String OPENSHIFT_URL_PRO2 = "https://api.san01.san.pre.bo1.paas.cloudcenter.corp:8443";
private static String OPENSHIFT_TOKEN_PRO1 = "iFSWsIultE4Chx9z1SsU8WrNIqvfiYp5OqYj-kUfcXA";
private static String OPENSHIFT_TOKEN_PRO2 = "iFSWsIultE4Chx9z1SsU8WrNIqvfiYp5OqYj-kUfcXA";
private static String GIT_TEMPLATES_URL = "https://github.alm.europe.cloudcenter.corp/sanes-automot/Selenium_YamlTemplates.git";
private static String GIT_TOKEN = "ca81b011-25cd-49d4-b482-93dcbbbfc66c"
private static String SELENIUM_URL_VM = "http://selenium-hub-san-automot-pro.appls.san01.san.pro.bo1.paas.cloudcenter.corp";
private static String SELENIUM_PORT = "80"

HashMap mapsParameters = new HashMap();
    mapsParameters.put('gitUrl',"${gitUrl}");
    mapsParameters.put('gitBranch',"${gitBranch}");
    mapsParameters.put('app',"${app}");
    mapsParameters.put('environment',"${environment}");
    mapsParameters.put('components',"${components}");
    mapsParameters.put('device',"${device}");
    mapsParameters.put('tags',"${tags}");
    mapsParameters.put('parallel',"${parallel}");
    mapsParameters.put('alm_flag',"${alm_flag}");
    mapsParameters.put('alm_domain',"${alm_domain}");
    mapsParameters.put('alm_project',"${alm_project}");
    mapsParameters.put('namespace',"${namespace}");
    
    int totalThreads=0;
    
    if("${parallel}" == 'false'){
        if(!mapsParameters.get('device').contains('chrome')){
            mapsParameters.put('nodoschrome',"0");
        }else{
            mapsParameters.put('nodoschrome',"1");            
        }
        if(!mapsParameters.get('device').contains('firefox')){
            mapsParameters.put('nodosfirefox',"0");
        }else{
            mapsParameters.put('nodosfirefox',"1");            
        }
        mapsParameters.put('threads','1');
    }else{
        if(!mapsParameters.get('device').contains('chrome')){
            mapsParameters.put('nodoschrome',"0");
        }else{
            mapsParameters.put('nodoschrome',"${threads}");   
        }
        if(!mapsParameters.get('device').contains('firefox')){
            mapsParameters.put('nodosfirefox',"0");
        }else{
            mapsParameters.put('nodosfirefox',"${threads}");            
        }
        if(mapsParameters.get('device').contains('iexplorer')){
            totalThreads="${threads}".toInteger();
        }
        totalThreads = totalThreads.toInteger() + mapsParameters.get('nodosfirefox').toInteger()+mapsParameters.get('nodoschrome').toInteger();
        mapsParameters.put('threads',totalThreads);
    }

    mapsParameters.put('grid_url_vm',SELENIUM_URL_VM);
    mapsParameters.put('grid_port',SELENIUM_PORT);



class OpenShiftRestClient{
    private static String OPENSHIFT_URL;
    private static String OPENSHIFT_TOKEN;
    def script;
    
    public OpenShiftRestClient(def script, String url, String token){
        this.script=script;
        this.OPENSHIFT_URL=url;
        this.OPENSHIFT_TOKEN=token;
    }
    
    public void setOpenshiftUrlAndToken(String url, String token){
        this.OPENSHIFT_URL=url;
        this.OPENSHIFT_TOKEN=token;
    }
    
    public String getDeploymentConfig(String namespace, String name) {
        this.script.sh("set +x\n curl -X GET " + this.OPENSHIFT_URL + "/oapi/v1/namespaces/" + namespace +"/deploymentconfigs/" + name + 
            "  -k " + 
            "  -H 'Accept: */*'" + 
            "  -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" + 
            "  -H 'Cache-Control: no-cache'" + 
            "  -H 'Connection: keep-alive'" + 
            "  -H 'cache-control: no-cache' > .resultCurl");
        String resultCurlFile = this.script.readFile(file: ".resultCurl").trim();
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
    }
    
   public String createDeploymentConfig(String namespace, String jsonRequest) {
        this.script.sh("set +x\n curl -k " + 
            "    -X POST " + this.OPENSHIFT_URL + "/apis/apps.openshift.io/v1/namespaces/" + namespace + "/deploymentconfigs" + 
            "    -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" + 
            "    -H 'Accept: application/json' " + 
            "    -H 'Content-Type: application/json' " + 
            "    -d '" + jsonRequest + "' > .resultCreateCurl");
        String resultCurlFile = this.script.readFile(file: ".resultCreateCurl").trim();
        this.script.echo("-----CREATE DEPLOY CONFIG RESULT-----\n"+resultCurlFile);
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
   }
   
   public String updateDeploymentConfig(String namespace, String applicationName, String jsonRequest) {
        this.script.sh("set +x\n curl -k " + 
            "    -X PUT " + this.OPENSHIFT_URL + "/apis/apps.openshift.io/v1/namespaces/" + namespace + "/deploymentconfigs/" + applicationName + 
            "    -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" + 
            "    -H 'Accept: application/json' " + 
            "    -H 'Content-Type: application/json' " + 
            "    -d '" + jsonRequest + "' > .resultUpdateCurl");
        String resultCurlFile = this.script.readFile(file: ".resultUpdateCurl").trim();
        this.script.echo("-----UPDATE DEPLOY CONFIG RESULT-----\n"+resultCurlFile);
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
   }
   
   public String getService(String namespace, String serviceName) {
        this.script.sh("set +x\n curl -X GET " + this.OPENSHIFT_URL + "/api/v1/namespaces/" + namespace +"/services/" + serviceName +
            "  -k " +
            "  -H 'Accept: */*'" +
            "  -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" +
            "  -H 'Cache-Control: no-cache'" +
            "  -H 'Connection: keep-alive'" +
            "  -H 'cache-control: no-cache' > .resultCurl");
        String resultCurlFile = this.script.readFile(file: ".resultCurl").trim();
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
   }
   
   public String updateService(String namespace, String serviceName, String jsonRequest) {
        this.script.sh("set +x\n curl -k " +
            "    -X PATCH " + this.OPENSHIFT_URL + "/api/v1/namespaces/" + namespace + "/services/" + serviceName +
            "    -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" +
            "    -H 'Accept: application/json' " +
            "    -H 'Content-Type: application/merge-patch+json' " +
            "    -d '" + jsonRequest + "' > .resultUpdateCurl");
        String resultCurlFile = this.script.readFile(file: ".resultUpdateCurl").trim();
        this.script.echo("-----UPDATE SERVICE RESULT-----\n"+resultCurlFile);
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
   }
   
   public String createService(String namespace, String jsonRequest) {
        this.script.sh("set +x\n curl -k " +
            "    -X POST " + this.OPENSHIFT_URL + "/api/v1/namespaces/" + namespace + "/services/" +
            "    -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" +
            "    -H 'Accept: application/json' " +
            "    -H 'Content-Type: application/json' " +
            "    -d '" + jsonRequest + "' > .resultUpdateCurl");
        String resultCurlFile = this.script.readFile(file: ".resultUpdateCurl").trim();
        this.script.echo("-----CREATE SERVICE RESULT-----\n"+resultCurlFile);
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
   }
   
   public String createRoute(String namespace, String jsonRequest) {
        this.script.sh("set +x\n curl -k " +
            "    -X POST " + this.OPENSHIFT_URL + "/apis/route.openshift.io/v1/namespaces/" + namespace + "/routes/" +
            "    -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" +
            "    -H 'Accept: application/json' " +
            "    -H 'Content-Type: application/json' " +
            "    -d '" + jsonRequest + "' > .resultUpdateCurl");
        String resultCurlFile = this.script.readFile(file: ".resultUpdateCurl").trim();
        this.script.echo("-----CREATE ROUTE RESULT-----\n"+resultCurlFile);
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
   }
   
   protected String getRoute(String namespace, String serviceName) {
        this.script.sh("set +x\n curl -X GET " + this.OPENSHIFT_URL + "/apis/route.openshift.io/v1/namespaces/" + namespace +"/routes/" + serviceName +
            "  -k " +
            "  -H 'Accept: */*'" +
            "  -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" +
            "  -H 'Cache-Control: no-cache'" +
            "  -H 'Connection: keep-alive'" +
            "  -H 'cache-control: no-cache' > .resultCurl");
        String resultCurlFile = this.script.readFile(file: ".resultCurl").trim();
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
   }
   
    public String getQuota(String namespace) {
        this.script.sh("set +x\n curl -X GET " + this.OPENSHIFT_URL + "/api/v1/namespaces/" + namespace +"/resourcequotas" +
            "  -k " + 
            "  -H 'Accept: */*'" + 
            "  -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" + 
            "  -H 'Cache-Control: no-cache'" + 
            "  -H 'Connection: keep-alive'" + 
            "  -H 'cache-control: no-cache' > .resultCurl");
        String resultCurlFile = this.script.readFile(file: ".resultCurl").trim();
        catchErrors(resultCurlFile,namespace);
        return resultCurlFile;
    }
    
    public boolean checkAvailableQuota(String namespace, int podsRequierd, int deploymentConfigRequired, int servicesRequired){
        String jsonResultGetQuota;

        jsonResultGetQuota = getQuota(namespace);
        def json = this.script.readJSON(text: jsonResultGetQuota.trim());
        def jsonStatusHard = json.items[0].status.hard;
        def jsonStatusUsed = json.items[0].status.used;
        
        int deploymentConfigAvailables=jsonStatusHard["count/deploymentconfigs.apps.openshift.io"].toInteger()-jsonStatusUsed["count/deploymentconfigs.apps.openshift.io"].toInteger();
        int podsAvailabes=jsonStatusHard["count/pods"].toInteger()-jsonStatusUsed["count/pods"].toInteger();
        int replicationsAvailables=jsonStatusHard["count/replicationcontrollers"].toInteger()-jsonStatusUsed["count/replicationcontrollers"].toInteger();
        int servicesAvailables=jsonStatusHard["count/services"].toInteger()-jsonStatusUsed["count/services"].toInteger();
        this.script.echo("QUOTA AVAILABLE in namespace "+namespace+" of " + this.OPENSHIFT_URL + ".");
        this.script.echo("DeploymentConfig Availables: " + deploymentConfigAvailables);
        this.script.echo("Pods Availables: " + podsAvailabes);
        this.script.echo("Replication Controllers Availables: " + replicationsAvailables);
        this.script.echo("Services Availables: " + servicesAvailables);

        if(deploymentConfigAvailables<deploymentConfigRequired){
            this.script.echo("=====NOT AVAILABLE QUOTA FOR DEPLOYMENT CONFIG=====");
            return false;
        }else if(replicationsAvailables<deploymentConfigRequired){
            this.script.echo("=====NOT AVAILABLE QUOTA FOR REPLICATION CONTROLLERS=====");
            return false;
        }else if(podsAvailabes<podsRequierd){
            this.script.echo("=====NOT AVAILABLE QUOTA FOR PODS=====");
            return false;
        }else if(servicesAvailables<servicesRequired){
            this.script.echo("=====NOT AVAILABLE QUOTA FOR SERVICES=====");
            return false;
        }else{
            this.script.echo("=====QUOTA AVAILABLE FOR DEPLOYMENT=====");
            return true;
        }
    }
    
    public void deployValidation(String namespace, String application) {
        String jsonResultGetDC;
        int count = 0;
        boolean successDeploy = false;
        while (count < 30 && !successDeploy) {
            jsonResultGetDC = getDeploymentConfig(namespace, application);

            def json = this.script.readJSON(text: jsonResultGetDC.trim());
            def jsonConditions = json.status.conditions;
            jsonConditions.each {
                if (it.type.toString().contains("Progressing") && it.message.toString().contains("successfully rolled out")) {
                    //Verbose.getIntance().debug("Success deploy");
                    this.script.echo("Success deploy");
                    successDeploy = true;
                }
                //Verbose.getIntance().debug("type: ", it.type);
                //Verbose.getIntance().debug("message: ",  it.message);
                //Verbose.getIntance().debug("count: " + count);
                this.script.echo("type: "+ it.type);
                this.script.echo("message: "+  it.message);
                this.script.echo("count: " + count);
            }
            if (successDeploy) {
                break;
            }
            this.script.sleep(10);
            count++;
        }
        if (!successDeploy) {
            throw new Exception("UNABLE TO CHECK DEPLOY STATUS. PLEASE, VERIFY IN OPENSHIFT.");
        }
    }
    
    public String getUrlRoute(String namespace, String application){
        String jsonResultGetRoute;
        jsonResultGetRoute = getRoute(namespace, application);
        def json = this.script.readJSON(text: jsonResultGetRoute.trim());
        def url = "http://"+json.spec.host;
        this.script.echo("Route URL: "+url);
        return url;
    }
    
    public String deleteDeploymentConfig(String namespace, String name) {
		this.script.sh("set +x\n curl -X DELETE " + this.OPENSHIFT_URL + "/apis/apps.openshift.io/v1/namespaces/" + namespace +"/deploymentconfigs/" + name + 
			"  -k " + 
			"  -H 'Accept: */*'" + 
			"  -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" + 
			"  -H 'Cache-Control: no-cache'" + 
			"  -H 'Connection: keep-alive'" + 
			"  -H 'cache-control: no-cache' > .resultCurl");
		String resultCurlFile = this.script.readFile(file: ".resultCurl").trim();
		this.script.echo("-----DELETE DEPLOY CONFIG RESULT-----\n"+resultCurlFile);
		return resultCurlFile;
	}
	
    public String deleteService(String namespace, String name) {
		this.script.sh("set +x\n curl -X DELETE " + this.OPENSHIFT_URL+ "/api/v1/namespaces/" + namespace +"/services/" + name + 
			"  -k " + 
			"  -H 'Accept: */*'" + 
			"  -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" + 
			"  -H 'Cache-Control: no-cache'" + 
			"  -H 'Connection: keep-alive'" + 
			"  -H 'cache-control: no-cache' > .resultCurl");
		String resultCurlFile = this.script.readFile(file: ".resultCurl").trim();
		this.script.echo("-----DELETE SERVICE RESULT-----\n"+resultCurlFile);
		return resultCurlFile;
	}
	
    public String deleteRoute(String namespace, String name) {
		this.script.sh("set +x\n curl -X DELETE " + this.OPENSHIFT_URL + "/oapi/v1/namespaces/" + namespace +"/routes/" + name + 
			"  -k " + 
			"  -H 'Accept: */*'" + 
			"  -H 'Authorization: Bearer " + this.OPENSHIFT_TOKEN + "'" + 
			"  -H 'Cache-Control: no-cache'" + 
			"  -H 'Connection: keep-alive'" + 
			"  -H 'cache-control: no-cache' > .resultCurl");
		String resultCurlFile = this.script.readFile(file: ".resultCurl").trim();
		this.script.echo("-----DELETE ROUTE RESULT-----\n"+resultCurlFile);
		return resultCurlFile;
	}
	
	
	private catchErrors(String resultCurlFile, String namespace){
	    if(resultCurlFile.contains("Unauthorized") || resultCurlFile.contains("no RBAC policy matched")){
            this.script.echo("UNAUTHORIZED USER");
            this.script.currentBuild.result = 'FAILED';
            this.script.error('Unauthorized user in namespace '+namespace+' of '+this.OPENSHIFT_URL);
	    }
	    if(resultCurlFile.contains("AlreadyExists")){
            this.script.echo("INFRA ALREADY EXISTS");
            this.script.currentBuild.result = 'UNSTABLE';
            this.script.error('Required infra already exists in namespace '+namespace+' of '+this.OPENSHIFT_URL);
	    }
	    
	    
	}
}

class SeleniumRestClient{
    
    private static String SELENIUM_URL;
    private static String SELENIUM_PORT;
    def script;
    
    public SeleniumRestClient(def script, String url, String port){
        this.script=script;
        this.SELENIUM_URL=url;
        this.SELENIUM_PORT=port;
    }
    
	public String getGridStatus(String gridUrl) {
      this.script.sh("set +x\n curl -X GET " + gridUrl + "/grid/api/hub/" +
            "  -H 'Cache-Control: no-cache'" + 
            "  -H 'Connection: keep-alive'" + 
            "  -H 'cache-control: no-cache' > .resultCurl");
      String resultCurlFile = readFile(".resultCurl").trim();
      return resultCurlFile;
	}
	
	public void checkSeleniumGridNodes(String gridUrl,String totalNodes){
        String jsonResultGetDC;
        int count = 0;
        boolean successDeploy = false;
        while (count < 30 && !successDeploy) {
            jsonResultGetDC = getGridStatus(gridUrl);

            def json = readJSON(text: jsonResultGetDC.trim());
            if (json.slotCounts.total.toString().contains(totalNodes)) {
                    echo("All nodes availables");
                    successDeploy = true;
                }else{
                echo("Waiting for "+totalNodes+" availables.");
                }
                echo("total nodos: "+ json.slotCounts.total);
                echo("nodos disponibles: "+  json.slotCounts.free);
                echo("count: " + count);
            if (successDeploy) {
                break;
            }
            sleep(5);
            count++;
        }
        if (!successDeploy) {
            throw new Exception("UNABLE TO CHECK NODES AVAILABLES. PLEASE, VERIFY IN SELENIUM GRID.");
        }
	}
    
    
}

class FunctionalTests{
    
	private HashMap<String,String> mapParameters;
	private GIT_TOKEN;
	private String grid_url;
    def script;
	
	public FunctionalTests(def script, HashMap<String,String> map, String gitToken) {
		this.script=script;
		this.mapParameters=map;
		this.GIT_TOKEN=gitToken;
	}
    
    
    public String launchTest() {
    	this.script.sh '''mvn clean install -U exec:java \
    	-Dexec.mainClass="es.santander.main.Init" \
    	-Dapplication.name="'''+this.mapParameters.get('app')+'''" \
    	-Denvironment="'''+this.mapParameters.get('environment')+'''" \
    	-Dapplication.components="'''+this.mapParameters.get('components')+'''" \
    	-Dbuild.number="'''+this.script.BUILD_NUMBER+'''" \
    	-Dwrokspace.dir="'''+this.script.WORKSPACE+'''" \
    	-Dexecutor.url="'''+this.script.BUILD_URL+'''" \
    	-Ddevice="'''+this.mapParameters.get('device')+'''" \
    	-Dtags="'''+this.mapParameters.get('tags')+'''" \
    	-Dparallel="'''+this.mapParameters.get('parallel')+'''" \
    	-Dthreads="'''+this.mapParameters.get('threads')+'''" \
    	-Dgridurl="'''+this.grid_url+'''" \
    	-Dgridurl_vm="'''+this.mapParameters.get('grid_url_vm')+'''" \
    	-Dgridport="'''+this.mapParameters.get('grid_port')+'''" \
    	-Dalm_flag="'''+this.mapParameters.get('alm_flag')+'''" \
    	-Dalm_domain="'''+this.mapParameters.get('alm_domain')+'''" \
    	-Dalm_project="'''+this.mapParameters.get('alm_project')+'''" \
    	-DNEXUS_PROXY_URL=https://nexus.alm.europe.cloudcenter.corp/repository/maven-public \
    	-Djavax.net.ssl.trustStore="mavenKeystore"'''
    	
        this.script.sh('cat results.json');
    }
    
    public void checkout(){
        this.script.git(
            url: this.mapParameters.get('gitUrl'),
            credentialsId: this.GIT_TOKEN,
            branch: this.mapParameters.get('gitBranch')
        )        
    }
    
    public void setGridUrl(String grid_url){
        this.grid_url=grid_url;
    }
}

class GetTemplates{

    private String GIT_TEMPLATES_URL;
    private String GIT_TOKEN;
  	private String deployHubJson;
  	private String serviceHubJson;
  	private String routeHubJson;
  	private String deployChromeJson;
  	private String deployFirefoxJson;
  	private HashMap mapParams;
    private HashMap deployHubParams = new HashMap();
    private HashMap deployNodeChromeParams = new HashMap();
    private HashMap deployNodeFirefoxParams = new HashMap();
    def script;


    public GetTemplates(def script, HashMap<String,String> map, String gitUrl, String gitToken){
        this.script=script;
        this.mapParams=map;
        this.GIT_TEMPLATES_URL=gitUrl;
        this.GIT_TOKEN=gitToken;
    }
    
    public void checkoutTemplates(){
        this.script.git(
			url: this.GIT_TEMPLATES_URL,
			credentialsId: this.GIT_TOKEN,
			branch: "master"
		)
    }

    public void readAndParseTemplates(){
        setDeployNames();
        this.deployHubJson = defDeployNames(this.script.readFile(file: "templates-selenium/selenium-hub-deployment-config.json").trim(),this.deployHubParams);
        this.serviceHubJson = defDeployNames(this.script.readFile(file: "templates-selenium/selenium-hub-service-config.json").trim(),this.deployHubParams);
        this.routeHubJson = defDeployNames(this.script.readFile(file: "templates-selenium/selenium-hub-route-config.json").trim(),this.deployHubParams);
        this.deployChromeJson = defDeployNames(this.script.readFile(file: "templates-selenium/selenium-node-chrome.json").trim(),this.deployNodeChromeParams);
        this.deployFirefoxJson = defDeployNames(this.script.readFile(file: "templates-selenium/selenium-node-firefox.json").trim(),this.deployNodeFirefoxParams);
    }
    
    private void setDeployNames(){
        this.deployHubParams.put("seleniumhubname","selenium-hub-"+this.mapParams.get('app').toLowerCase()+"-"+this.mapParams.get('components').toLowerCase());
        this.deployNodeChromeParams.put("seleniumhubaddress","selenium-hub-"+this.mapParams.get('app').toLowerCase()+"-"+this.mapParams.get('components').toLowerCase());
        this.deployNodeChromeParams.put("seleniumnodechromename","selenium-node-chrome-"+this.mapParams.get('app').toLowerCase()+"-"+this.mapParams.get('components').toLowerCase());
        this.deployNodeChromeParams.put("seleniumnodechromereplicas",this.mapParams.get('nodoschrome'));
        this.deployNodeFirefoxParams.put("seleniumhubaddress","selenium-hub-"+this.mapParams.get('app').toLowerCase()+"-"+this.mapParams.get('components').toLowerCase());
        this.deployNodeFirefoxParams.put("seleniumnodefirefoxname","selenium-node-firefox-"+this.mapParams.get('app').toLowerCase()+"-"+this.mapParams.get('components').toLowerCase());
        this.deployNodeFirefoxParams.put("seleniumnodefirefoxreplicas",this.mapParams.get('nodosfirefox'));
    }

    private String defDeployNames(String json, HashMap<String, String> map) {
		for (entry in map) {
		    this.script.echo("Interpolation values of json: add to "+entry.getKey()+ " value: "+entry.getValue());
		    json=json.replaceAll(entry.getKey(), entry.getValue());
		}
		return json;
	}
	
	public String getDeployHubJson() {
		return deployHubJson;
	}
	public String getServiceHubJson() {
		return serviceHubJson;
	}
	public String getRouteHubJson() {
		return routeHubJson;
	}
	public String getDeployChromeJson() {
		return deployChromeJson;
	}
	public String getDeployFirefoxJson() {
		return deployFirefoxJson;
	}
	public HashMap<String, String> getDeployHubParams() {
		return deployHubParams;
	}
	public HashMap<String, String> getDeployNodeChromeParams() {
		return deployNodeChromeParams;
	}
	public HashMap<String, String> getDeployNodeFirefoxParams() {
		return deployNodeFirefoxParams;
	}
}


OpenShiftRestClient osrest = new OpenShiftRestClient(this,OPENSHIFT_URL_PRO1,OPENSHIFT_TOKEN_PRO1);
FunctionalTests functtest = new FunctionalTests(this,mapsParameters,GIT_TOKEN);
GetTemplates templates = new GetTemplates(this,mapsParameters,GIT_TEMPLATES_URL,GIT_TOKEN);

def stage(name, execute, block) {
    return stage(name, execute ? block : {echo "skipped stage $name"})
}

//Functional tests execution with deploy and delete required infra
node ("MavenPod") {
    properties([
      parameters([
		string(name: 'app', defaultValue: 'Pipe', description: 'Application on the execution of functional tests.',required: true ),
        string(name: 'components', defaultValue: 'Pipe', description: 'Name of components or micro.',required: true ),
        choice(name: 'environment', choices: ['PRE', 'PRO', 'DEV'], description: 'Test environment.',required: true ),
        choice(name: 'device', choices: ['chrome', 'firefox', 'iexplorer','chrome;firefox','chrome;iexplorer','firefox;iexplorer','chrome;firefox;iexplorer'], description: 'Device or devices on run test.',required: true ),
        string(name: 'tags', defaultValue: '', description: 'Run tags.'),
        booleanParam(name: 'parallel', defaultValue: 'false', description: 'Enable parallel mode.'),
        string(name: 'threads', defaultValue: '1', description: 'Number of threads.'),
        booleanParam(name: 'alm_flag', defaultValue: 'false', description: 'Enable report to ALM.'),
        string(name: 'alm_domain', defaultValue: 'QAF_SAN_AUTOMATIZACION', description: 'ALM Domain.'),
        string(name: 'alm_project', defaultValue: 'VOSTOK', description: 'ALM Project.'),
        string(name: 'namespace', defaultValue: 'san-altm-pre', description: 'Namespace of OpenShift on deploy Selenium-Grid.',required: true),
        string(name: 'gitUrl', defaultValue: 'https://x281095@github.alm.europe.cloudcenter.corp/sanes-vostok/functional-test.git', description: 'Functional tests repository url.',required: true),
        string(name: 'gitBranch', defaultValue: 'feature-sprint5', description: 'Functional tests repository branch.',required: true)
        ])
    ])

  	stage('Checkout Selenium Grid Templates',mapsParameters.get('device') != 'iexplorer'){
  	    templates.checkoutTemplates();
        templates.readAndParseTemplates();
  	}
	
  	stage('Get avaliable openshift quota',mapsParameters.get('device') != 'iexplorer'){
        int podsRequired = mapsParameters.get('nodoschrome').toInteger()+ mapsParameters.get('nodosfirefox').toInteger()+1;
        int deploymentConfigRequired = 0;
        if(mapsParameters.get('nodoschrome')!=0 && mapsParameters.get('nodosfirefox')==0){
            deploymentConfigRequired = 2;
        }else{
            deploymentConfigRequired = 3;
        }
    
        if(!osrest.checkAvailableQuota(mapsParameters.get('namespace'),podsRequired,deploymentConfigRequired,1)){
            echo("NOT AVAILABLE QUOTA ON PRO1");
            echo("SWITCH TO PRO2");
            osrest.setOpenshiftUrlAndToken(OPENSHIFT_URL_PRO2,OPENSHIFT_TOKEN_PRO2);
            if(!osrest.checkAvailableQuota(mapsParameters.get('namespace'),podsRequired,deploymentConfigRequired,1)){
                echo("NOT AVAILABLE QUOTA ON PRO2");
                currentBuild.result = 'FAILED'
                error('There is no quota available in namespace '+mapsParameters.get('namespace')+' to build the infrastructure necessary for execution')
            }
        }
    }

try{
  	stage('Deploy Selenium Grid',mapsParameters.get('device') != 'iexplorer'){
        echo("======CREATE SELENIUM HUB DEPLOYMENT======");
        osrest.createDeploymentConfig(mapsParameters.get('namespace'),templates.getDeployHubJson());
        echo("======CREATE SELENIUM HUB SERVICE======");
        osrest.createService(mapsParameters.get('namespace'),templates.getServiceHubJson());
        echo("======CREATE SELENIUM HUB ROUTE======");
        osrest.createRoute(mapsParameters.get('namespace'),templates.getRouteHubJson());  	        
        if(mapsParameters.get('device').contains('chrome')){
            echo("======CREATE SELENIUM NODE CHROME DEPLOYMENT======");
            osrest.createDeploymentConfig(mapsParameters.get('namespace'),templates.getDeployChromeJson());
            osrest.deployValidation(mapsParameters.get('namespace'), templates.getDeployNodeChromeParams().get("seleniumnodechromename"));
        }
        if(mapsParameters.get('device').contains('firefox')){
            echo("======CREATE SELENIUM NODE FIREFOX DEPLOYMENT======");
            osrest.createDeploymentConfig(mapsParameters.get('namespace'),templates.getDeployFirefoxJson());   
            osrest.deployValidation(mapsParameters.get('namespace'), templates.getDeployNodeFirefoxParams().get("seleniumnodefirefoxname"));
        }
        osrest.deployValidation(mapsParameters.get('namespace'), templates.getDeployHubParams().get("seleniumhubname"));
        functtest.setGridUrl(osrest.getUrlRoute(mapsParameters.get('namespace'), templates.getDeployHubParams().get("seleniumhubname")));
        echo('=====SELENIUM GRID DEPLOY SUCCESS=====');
        sleep(10);
    }
    stage('Launch functional test'){
        functtest.checkout();
        functtest.launchTest();
    }
}catch(e){
    echo('Execution Failed');
    throw e;
}finally{
  	stage('Delete Infra',mapsParameters.get('device') != 'iexplorer' && "${currentBuild.result}" != "UNSTABLE"){
  	    echo("${currentBuild.result}");
        osrest.deleteDeploymentConfig(mapsParameters.get('namespace'),templates.getDeployHubParams().get("seleniumhubname"));
        osrest.deleteService(mapsParameters.get('namespace'),templates.getDeployHubParams().get("seleniumhubname"));
        osrest.deleteRoute(mapsParameters.get('namespace'),templates.getDeployHubParams().get("seleniumhubname")); 	        
  	    if(mapsParameters.get('device').contains('chrome')){
            osrest.deleteDeploymentConfig(mapsParameters.get('namespace'),templates.getDeployNodeChromeParams().get("seleniumnodechromename"));
        }
        if(mapsParameters.get('device').contains('firefox')){
            osrest.deleteDeploymentConfig(mapsParameters.get('namespace'),templates.getDeployNodeFirefoxParams().get("seleniumnodefirefoxname"));
        }
        echo('=====SELENIUM GRID DELETE SUCCESS=====');
  	}
}
}
