node {
	currentBuild.displayName = "1.${BUILD_NUMBER}"
	def GIT_COMMIT
  stage ('cloning the repository'){
     def scm =  git 'https://github.com/hclpowership/PetClinic'
	    GIT_COMMIT = sh(returnStdout: true, script: "git rev-parse HEAD").trim()
	  echo "AAAA ${GIT_COMMIT}"
  }
	
  stage ('Build') {
      withMaven(jdk: 'JDK_local', maven: 'MVN_Local') {
     
      sh 'mvn clean package'
	      
    }
  }

stage('Publish artificats to UrbanCode Deploy'){
   step([$class: 'UCDeployPublisher',
        siteName: 'ucd-server',
        component: [
            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
            componentName: 'PetClinicComponent',
            createComponent: [
                $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
                componentTemplate: '',
                componentApplication: 'PetClinic'
            ],
            delivery: [
                $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
                pushVersion: '1.${BUILD_NUMBER}',
                baseDir: '/var/jenkins_home/workspace/${JOB_NAME}/target',
                fileIncludePatterns: '*.war',
                fileExcludePatterns: '',
               // pushProperties: 'jenkins.server=Jenkins-app\njenkins.reviewed=false',
                pushDescription: 'Pushed from Jenkins'
            ]
        ]
    ])
	
		 // echo "Demo1234 ${PetClinic_cmp_VersionId}"
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


}
