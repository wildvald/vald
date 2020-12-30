#!/usr/bin/env groovy
// vars para deploy, com autenticação do jenkins e hostname do servidor tomcat

pipeline 
{
	agent any

	environment 
	{
		CHECK_URL = "Http://localhost:8082/cms-1.0"
		CURL = "curl -u ${env.TOMCAT_USERNAME}:${env.TOMCAT_PASSWORD} --upload-file \"build/libs/cms.1.0.war\" \"http://localhost:8082/\""
		TOMCAT_USER= "admin"
		TOMCAT_PWD = "odsoft"
		TOMCAT_HOST = "localhost:8082"
		GIT_USERNAME= ""
		GIT_PASSWORD= ""
	}
	stages
	{
		stage('Checkout')
		{
			steps
			{
				checkoutFunction()
			}
		}

		stage ('Build')
		{
			steps {
				dir ('odsoft/exercise2/component3/cms') 
				{
					buildFunction()
					archiveArtifactsFunction()                    
				}
			}
		}
		
		/*stage ('Javadoc')
		{
			steps 
			{
				dir ('odsoft/exercise2/component3/cms') 
				{
					generateAndPublishJavadoc()
				}
			}
		}
		
		stage('UnitTests')
		{
			steps
			{
				dir ('odsoft/exercise2/component3/cms') 
				{
					unitTestsFunction()
				}
			}
		}
		stage ('IntegrationTests')
		{
			steps 
			{
				dir ('odsoft/exercise2/component3/cms')
				{
					integrationTestsFunction()
				}
			}
		}

		stage ('MutationTests')
		{
			steps 
			{
				dir ('odsoft/exercise2/component3/cms')
				{
					mutationTestsFunction()
				}
			}
		}        

		stage ('Tests reports')
		{
			steps 
			{
				dir ('odsoft/exercise2/component3/cms') 
				{
					junitTestsReport()
					jacocoReport()
				}
			}
		}*/    
		stage ('System Test_Deploy_tomcat')
		{
			steps 
			{
				dir ('odsoft/exercise2/component3/cms') 
				{
					deployToTomcat()
				}        
			}
		}    
		/*stage ('SmokeTest')
		{
			steps 
			{
				dir ('odsoft/exercise2/component3/cms') 
				{
					smokeTest()
				}        
			}
		} 
		stage ('UIAcceptanceTests') 
		{
			steps 
			{
				echo 'UIAcceptanceTest'
				manualUIAcceptanceTest()
			}                
		}    
		stage ('ContinuousIntegrationFeedback')
		{
			steps 
			{
				tagRepository()
			}                
		}   */ 
	}        
	post
	{
		success
		{
			echo 'Build successful - tagging repository...'
		}
		failure
		{
			echo 'Build failed - tagging repository...'

		}
		aborted
		{
			echo 'Build failed - tagging repository...'
		}
	}
}

void checkoutFunction()
{
	echo "Checking out..."
	//comparação
	//checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '4befa063-3d0b-4e47-93a6-1fb984910521', url: 'https://1171490@bitbucket.org/mei-isep/odsoft-20-21-rcc-g411.git']]])
	checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'a77945e3-de62-4b4b-a72b-1feb414e20c1', url: 'https://wildvald@bitbucket.org/mei-isep/odsoft-20-21-rcc-g411.git']]])

}

void buildFunction()
{
	echo 'Building...'
	if (isUnix())  
	{
		sh './gradlew build'
	} else 
	{
		bat 'gradlew build'
	}
	
}

void archiveArtifactsFunction() 
{
	echo 'Archiving artifacts ...'
	archiveArtifacts artifacts: 'build/libs/cms-1.0.war', fingerprint: true, onlyIfSuccessful: true, followSymlinks: false
}

void generateAndPublishJavadoc() 
{
	echo 'Generating javadoc...'
	if (isUnix()) 
	{
		sh './gradlew javadoc'
	} else
	{
		bat 'gradlew javadoc'
	}
	echo 'Publishing javadoc ...'
	javadoc javadocDir: 'build/docs/javadoc', keepAll: false
}

void unitTestsFunction() 
{
	echo 'Unit testing ...'
	if (isUnix()) 
	{
		sh './gradlew cleanTest test'
	} else 
	{
		bat 'gradlew cleanTest test'
	}
	echo 'Publishing unit tests results report ...'
	publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'build/reports/test', reportFiles: 'index.html', reportName:''])
}

void integrationTestsFunction() 
{
	echo 'Testing integration ...'
	if (isUnix()) 
	{
		sh './gradlew cleanIntegrationTest integrationTest'

	} else 
	{
		bat 'gradlew cleanIntegrationTest integrationTest'
	}
	echo 'Publishing integration tests results report ...'
	publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'build/reports/integrationTest', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: 'integrationTest'])
}
void mutationTestsFunction() 
{
	echo 'Mutation tests ...'
	if (isUnix()) 
	{
		sh './gradlew pitest'
	} 
	else 
	{
		bat 'gradlew pitest'
	}

	echo 'Publishing mutation tests results report ...'
	publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'build/reports/pitest',reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: 'mutationTest'])
}

void jacocoReport() 
{
	echo 'Publishing jacco coverage report ...'
	jacoco execPattern: '**/**.exec'
}

void junitTestsReport() 
{
	echo 'Publishing juint test reports ...'
	junit 'build/test-results/test/*.xml,build/test-results/integrationTest/*.xml'
}

void deployToTomcat() 
{
	echo 'Deploying to Tomcat ...'
	//withCredentials([usernamePassword(credentialsId: '95778ad7-5b10-4a60-8d0e-cbfb4d8550b3', passwordVariable: 'TOMCAT_PASSWORD', usernameVariable: 'TOMCAT_USERNAME')]) 
	withCredentials([usernamePassword(credentialsId: 'a77945e3-de62-4b4b-a72b-1feb414e20c1', passwordVariable: 'TOMCAT_PASSWORD', usernameVariable: 'TOMCAT_USERNAME')]) 
	{
		if(isUnix())
		{
			//sh "curl -u ${TOMCAT_USER}:${TOMCAT_PWD} -T build/libs/cms-1.0.war '${TOMCAT_HOST}/manager/text/deploy?path=/cms-1.0&update=true'"
		} else 
		{
			//bat "curl -u ${TOMCAT_USER}:${TOMCAT_PWD} -T build/libs/cms-1.0.war '${TOMCAT_HOST}/manager/text/deploy?path=/cms-1.0&update=true'"
			bat "curl -u ${TOMCAT_USER}:${TOMCAT_PWD} -T build/libs/cms-1.0.war '${TOMCAT_HOST}/webapps'"
		}
	}
}

void smokeTest() 
{
	echo 'Smoke Test...'
	if(isUnix())
	{
		sh "curl ${CHECK_URL}"
	} else 
	{
		bat "curl ${CHECK_URL}"
	}
	echo 'Sending email...'
	sendNotification()
}
void sendNotification()
{
	mail([
	body: "Validar a aplicação: http://localhost:8082/cms-1.0 Aprovação Manual: '${BUILD_URL}input/'",
	subject: 'JENKINS-NOTIFICATION: Build Successfull',
	to: 'odsoft.g411@gmail.com'])
}

void manualUiAcceptanceTest() 
{
	mail([
		body: "Validar a aplicação: http://localhost:8082/cms-1.0 Aprovação Manual: '${BUILD_URL}input/'",
		subject: 'JENKINS-NOTIFICATION: Build Successfull',
		to: 'odsoft.g411@gmail.com'
		])
	input ('The previous tests were executed with success:')    

}

void tagRepository() 
{
	echo 'Build successful - tagging repository ...'
	//withCredentials([usernamePassword(credentialsId: '4befa063-3d0b-4e47-93a6-1fb984910521', passwordVariable: '', usernameVariable: '')]) 
	withCredentials([usernamePassword(credentialsId: 'a77945e3-de62-4b4b-a72b-1feb414e20c1', passwordVariable: '', usernameVariable: '')]) 
	{
		if (isUnix())
		{
			sh("git tag -a 'Building${env.BUILD_NUMBER}-Passed' -m 'Jenkins'")
			sh("git push https://wildvald@bitbucket.org/mei-isep/odsoft-20-21-rcc-g411.git --tag")
		} 
		else 
		{
			bat ("git tag -a 'Building${env.BUILD_NUMBER}-Passed' -m 'Jenkins'")
			bat ("git push https://wildvald@bitbucket.org/mei-isep/odsoft-20-21-rcc-g411.git --tag")
		}
	}
}

