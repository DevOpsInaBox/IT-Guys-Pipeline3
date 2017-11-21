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
      
      echo "**********************************************************************************"    
      echo "* Moving $app from Integration to Testing from Development"
      echo "**********************************************************************************"
						
      def data = dh.moveApplication("http://rocket:8080","admin","admin",${app},"GLOBAL.My Pipeline.Development","Move to Integration");
      println(data[0]);
      println(data[1]);
      
      echo "**********************************************************************************"    
      echo "* Deploying $app to Integration"
      echo "**********************************************************************************"
						
						data = dh.deployApplication("http://rocket:8080","admin","admin",${app},"IT Guys Int");
      println(data[0]);
      println(data[1]);
						
      echo "**********************************************************************************"
      echo "* Running Testcases for $app in Integration"
      echo "**********************************************************************************"
      cmd = /runtestcases.py --app ${app} --env Integration/
      sh cmd
    }  
    
    stage ('Testing') {
      echo "**********************************************************************************"    
      echo "* Moving $app from Integration to Testing"
      echo "**********************************************************************************"
      def data = dh.moveApplication("http://rocket:8080","admin","admin",${app},"GLOBAL.My Pipeline.Initegration","Move to Testing");
      println(data[0]);
      println(data[1]);
      
      echo "**********************************************************************************"    
      echo "* Deploying $app to Integration"
      echo "**********************************************************************************"
						
						data = dh.deployApplication("http://rocket:8080","admin","admin",${app},"IT Guys Test");
      println(data[0]);
      println(data[1]);
						
      echo "**********************************************************************************"
      echo "* Running Testcases for $app in Test"
      echo "**********************************************************************************"
      cmd = /runtestcases.py --app ${app} --env Test/
      sh cmd
    }
    
    stage ('Production') {
      def data = dh.moveApplication("http://rocket:8080","admin","admin",${app},"GLOBAL.My Pipeline.Test","Move to Prodution");
      println(data[0]);
      println(data[1]);
      
      echo "**********************************************************************************"    
      echo "* Deploying $app to Production"
      echo "**********************************************************************************"
						
						data = dh.deployApplication("http://rocket:8080","admin","admin",${app},"IT Guys Prod");
      println(data[0]);
      println(data[1]);
    }
}
