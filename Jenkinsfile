node {

  // get code from repo & clean it (and stash it)
  stage('checkout') {

    // get code from repo - use generic command rather than actual git though
    checkout scm
		      
    // make sure we get clean stuff to copy around
    bat 'mvn clean'
			          
    // take a copy for other place(s)
    stash name: 'cleanWS',
          includes: '**'
			      
  }

  // Arbitary means to force this pipeline to spawn two parallel jobs
  // Need >2 executors to see the effect...
  parallel a: {  

    // build package + test reports + code coverage
    node {
				          
      stage('quality') {
								            
        unstash 'cleanWS'

        bat 'mvn package'

        jacoco()

        step([$class: 'JUnitResultArchiver', testResults: 'target/surefire-reports/TEST*.xml'])

      }  
    }
  }, b: {

    // install job
    node {  
      stage('build') {

        unstash 'cleanWS'

        try {

          bat 'mvn install'

        } catch (err) {

          echo "Caught error : ${err}"
          CurrentBuild.result = 'FAILURE'

        }

        archiveArtifacts 'target/*.jar'

      }   // stage
    }     // node
  }       // parallel
}


