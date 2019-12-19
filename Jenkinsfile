node {
  stage ('cloning the repository'){
      git 'https://github.com/hclpowership/PetClinic.git'
  }
	
  stage ('Build') {
      withMaven(jdk: 'JDK_for-agent', maven: 'MVN_Local') {
      sh "echo JAVA_HOME=$JAVA_HOME"
      sh 'mvn clean package'
	      
    }
  }

stage('Publish artificats to UrbanCode Deploy'){
   step([$class: 'UCDeployPublisher',
        siteName: 'ucd-server',
        component: [
            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
            componentName: 'PetClinic-cmp',
            createComponent: [
                $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
                componentTemplate: '',
                componentApplication: 'PetClinic'
            ],
            delivery: [
                $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
                pushVersion: 'Ver.${BUILD_NUMBER}',
                baseDir: '/home/jenkins/workspace/${JOB_NAME}/target',
                fileIncludePatterns: '*.war',
                fileExcludePatterns: '',
               // pushProperties: 'jenkins.server=Jenkins-app\njenkins.reviewed=false',
                pushDescription: 'Pushed from Jenkins'
            ]
        ]
    ])
	step([$class: 'UCDeployPublisher',
        	siteName: 'ucd-server',
        	deploy: [
            	$class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
            	deployApp: 'PetClinic',
            	deployEnv: 'Dev',
            	deployProc: 'Deploy-PetClinic',
            	createProcess: [
                	$class: 'com.urbancode.jenkins.plugins.ucdeploy.ProcessHelper$CreateProcessBlock',
                	processComponent: 'Deploy'
            	],
            	deployVersions: 'PetClinic-cmp:Ver.${BUILD_NUMBER}',
		//deployVersions: 'SNAPSHOT=Base Configuration',
            	deployOnlyChanged: false
        ]
    ])
} 
	
	
	}
	
	
	echo "(*****)"
	  echo "${UUID}"
	
	  echo "Demo1234 ${PetClinicComponent_VersionId}"
	  def newComponentVersionId = "${PetClinicComponent_VersionId}"
	  step($class: 'UploadBuild', tenantId: "5ade13625558f2c6688d15ce", revision: "${GIT_COMMIT}", appName: "PetClinic", requestor: "admin", id: "${newComponentVersionId}" )
	  echo "Demo123 ${newComponentVersionId}"
	sleep 25
	  step([$class: 'UCDeployPublisher',
		deploy: [ createSnapshot: [deployWithSnapshot: true, 
			 snapshotName: "1.${BUILD_NUMBER}"],
			 deployApp: 'PetClinic', 
			 deployDesc: 'Requested from Jenkins', 
			 deployEnv: 'PetClinic_Dev', 
			 deployOnlyChanged: false, 
			 deployProc: 'Deploy', 
			 deployReqProps: '', 
			 deployVersions: "PetClinicComponent:1.${BUILD_NUMBER}"], 
		siteName: 'ucd-server'])
}
