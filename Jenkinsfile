stage 'build'
node ('master'){
     git 'https://github.com/chaitanyavaddadi/webapp.git'
     withEnv(["PATH+MAVEN=${tool 'maven3.5.3'}/bin"]) {
          sh "mvn clean package"		  
     }
     stash excludes: 'target/', includes: '**', name: 'source'
}
stage 'test'
parallel 'integration': { 
     node ('master') {
          unstash 'source'
          withEnv(["PATH+MAVEN=${tool 'maven3.5.3'}/bin"]) {
               sh "mvn clean package"
          }
		  //
     }
}, 'functional': {
     node ('master') {
          unstash 'source'
          withEnv(["PATH+MAVEN=${tool 'maven3.5.3'}/bin"]) {
               sh "mvn clean package" //sonar:sonar
          }
     }
}
stage 'approve'
timeout(time: 7, unit: 'SECONDS') {
     input message: 'Do you want to deploy?', submitter: 'anilk'
}
stage name:'deploy', concurrency: 1
node ('master') {
     unstash 'source'
     withEnv(["PATH+MAVEN=${tool 'maven3.5.3'}/bin"]) {
          sh "mvn clean package"
     }
	 archiveArtifacts '**/*.war'
}
