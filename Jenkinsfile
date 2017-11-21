#!/usr/bin/env groovy

@Library('deployhub') _

def app=""
def env=""
def cmd=""

def dh = new deployhub();

node {
    
    stage('Clone sources') {
        git url: 'https://github.com/OpenMake-Software/IT-Guys-Pipeline.git'
    }
    
    stage ('Integration') {
      def lines=readFile('Deployfile').trim().split("\n");
      app=lines[1].split(':')[1].trim()
      env=lines[2].split(':')[1].trim() 
						app=app.substring(1, app.length() - 1)					  
 
      echo "**********************************************************************************"    
      echo "* Approving $app for Integration"
      echo "**********************************************************************************"
						
      def data = dh.approveApplication("http://rocket:8080","admin","admin", app);
      println(data[0]);
      println(data[1]);	     
						
      echo "**********************************************************************************"    
      echo "* Moving $app from Development to Integration"
      echo "**********************************************************************************"
						
      data = dh.moveApplication("http://rocket:8080","admin","admin", app ,"GLOBAL.My Pipeline.Development","Move to Integration");
      println(data[0]);
      println(data[1]);
      
      echo "**********************************************************************************"    
      echo "* Deploying $app to Integration"
      echo "**********************************************************************************"
						
						data = dh.deployApplication("http://rocket:8080","admin","admin", app, "IT Guys Int");
      println(data[0]);
      println(data[1]);
						
      echo "**********************************************************************************"
      echo "* Running Testcases for $app in Integration"
      echo "**********************************************************************************"
						cmd = "runtestcases.py --app \"${app}\" --env Integration"
      sh cmd
    }  
    
    stage ('Test') {
      def lines=readFile('Deployfile').trim().split("\n");
      app=lines[1].split(':')[1].trim()
      env=lines[2].split(':')[1].trim() 
						app=app.substring(1, app.length() - 1)					  
 
      echo "**********************************************************************************"    
      echo "* Moving $app from Integration to Testing"
      echo "**********************************************************************************"
						
      data = dh.moveApplication("http://rocket:8080","admin","admin", app ,"GLOBAL.My Pipeline.Integration","Move to Testing");
      println(data[0]);
      println(data[1]);
      
      echo "**********************************************************************************"    
      echo "* Deploying $app to Testing"
      echo "**********************************************************************************"
						
						data = dh.deployApplication("http://rocket:8080","admin","admin", app, "IT Guys Test");
      println(data[0]);
      println(data[1]);
						
      echo "**********************************************************************************"
      echo "* Running Testcases for $app in Testing"
      echo "**********************************************************************************"
						cmd = "runtestcases.py --app \"${app}\" --env Test"
      sh cmd
    }
				
   stage ('Prod') {
     def lines=readFile('Deployfile').trim().split("\n");
     app=lines[1].split(':')[1].trim()
     env=lines[2].split(':')[1].trim() 
					app=app.substring(1, app.length() - 1)					  

     echo "**********************************************************************************"    
     echo "* Moving $app from Testing to Prod"
     echo "**********************************************************************************"
					
     data = dh.moveApplication("http://rocket:8080","admin","admin", app ,"GLOBAL.My Pipeline.Testing","Move to Production");
     println(data[0]);
     println(data[1]);
     
     echo "**********************************************************************************"    
     echo "* Deploying $app to Prod"
     echo "**********************************************************************************"
					
					data = dh.deployApplication("http://rocket:8080","admin","admin", app, "IT Guys Prod");
     println(data[0]);
     println(data[1]);
   }	
}
