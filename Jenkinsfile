#!/usr/bin/env groovy

@Library('deployhub') _

def app=""
def environment=""
def cmd=""
def url=""
def user="admin"
def pw="admin"

def dh = new deployhub();

node {
    
    url = dh.getURL(env);
	
    stage('Clone sources') {
        git url: 'https://github.com/OpenMake-Software/IT-Guys-Pipeline.git'
    }
    
    stage ('Integration') {
      def lines=readFile('Deployfile').trim().split("\n");
      app=lines[1].split(':')[1].trim()
      environment=lines[2].split(':')[1].trim() 
      app=app.substring(1, app.length() - 1)       
 
      echo "Approving $app for Integration"
      
      def data = dh.approveApplication(url,user,pw, app);
      if (data[0])
      {
       echo "Moving $app from Development to Integration"
     
       data = dh.moveApplication(url,user,pw, app ,"GLOBAL.My Pipeline.Development","Move to Integration");
       if (data[0])
       {
        echo "Deploying $app to Integration"
        
        dh.forceDeployIfNeeded(url,user,pw, "IT Guys Int");
        
        data = dh.deployApplication(url,user,pw, app, "IT Guys Int");
        if (data[0])
        {
         def deploymentid = data[1]['deploymentid'];

         echo "Deployment Logs for #$deploymentid"
         data = dh.getLogs(url,user,pw, "$deploymentid");
         echo data[1];
         
         echo "Running Testcases for $app in Integration"
         cmd = "runtestcases.py --app \"${app}\" --env Integration --success 100 --build ${env.BUILD_NUMBER} --job \"${env.JOB_NAME}\""
         def r = sh script: cmd, returnStatus: true
	 echo "RC=$r"
		
	 if (r != 0) /* ROLLBACK */
	 {
          data = dh.deployApplication(url,user,pw, "IT Guys;2", "IT Guys Int");
          if (data[0])
          {
           deploymentid = data[1]['deploymentid'];

           echo "ROLLBACK: Deployment Logs for #$deploymentid"
           data = dh.getLogs(url,user,pw, "$deploymentid");
           echo data[1];
	  }
          sh "exit ${r}" 
	 }
        }
        else
        {
         error(data[1]);
        } 
       }
       else
       {
        error(data[1]);
       } 
      }
      else
      {
       error(data[1]);
      } 
    }  
    
    stage ('Test') {
      def lines=readFile('Deployfile').trim().split("\n");
      app=lines[1].split(':')[1].trim()
      environment=lines[2].split(':')[1].trim() 
      app=app.substring(1, app.length() - 1)       
 
      echo "Moving $app from Integration to Testing"
      
      data = dh.moveApplication(url,user,pw, app ,"GLOBAL.My Pipeline.Integration","Move to Testing");
      if (data[0])
      {
       echo "Deploying $app to Testing"
        
       dh.forceDeployIfNeeded(url,user,pw, "IT Guys Test");
              
       data = dh.deployApplication(url,user,pw, app, "IT Guys Test");
       if (data[0])
       {
        deploymentid = data[1]['deploymentid'];
        echo "Deployment Logs for #$deploymentid"
        data = dh.getLogs(url,user,pw,"$deploymentid");

        echo data[1];
      
        echo "Running Testcases for $app in Testing"
        cmd = "runtestcases.py --app \"${app}\" --env Testing --success 100 --build ${env.BUILD_NUMBER} --job \"${env.JOB_NAME}\""
         def r = sh script: cmd, returnStatus: true
	 echo "RC=$r"
	       
	 if (r != 0) /* ROLLBACK */
	 {
          data = dh.deployApplication(url,user,pw, "IT Guys;2", "IT Guys Test");
          if (data[0])
          {
           def deploymentid = data[1]['deploymentid'];

           echo "ROLLBACK: Deployment Logs for #$deploymentid"
           data = dh.getLogs(url,user,pw, "$deploymentid");
           echo data[1];
	  }
          sh "exit ${r}" 	  
	 }
       }
       else
       {
        error(data[1]);
       } 
      }
      else
      {
       error(data[1]);
      } 
    }
    
   stage ('Prod') {
     def lines=readFile('Deployfile').trim().split("\n");
     app=lines[1].split(':')[1].trim()
     environment=lines[2].split(':')[1].trim() 
     app=app.substring(1, app.length() - 1)       

     echo "Moving $app from Testing to Prod"
     
     data = dh.moveApplication(url,user,pw, app ,"GLOBAL.My Pipeline.Testing","Move to Production");
     if (data[0])
     { 
      echo "Deploying $app to Prod"
     
      dh.forceDeployIfNeeded(url,user,pw, "IT Guys Prod");     
     
      data = dh.deployApplication(url,user,pw, app, "IT Guys Prod");
      if (data[0])
      {
       def deploymentid = data[1]['deploymentid'];
       echo "Deployment Logs for #$deploymentid"
       data = dh.getLogs(url,user,pw,"$deploymentid");
       echo data[1];
      }
      else
      {
       error(data[1]);
      } 
     }
     else
     {
      error(data[1]);
     }
   } 
}
