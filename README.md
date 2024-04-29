pipeline{

       agent any
       tools{
            maven "maven3"
                 }
       environment{
            SONAR_HOME=tool "sonar-tool-task2"
                     }

        stages{
              
             stage("SCM CHECKOUT"){
                 steps{
                      git url: "https://github.com/chandu-987-git/springboot-hello.git",branch: "main"
                           }
                                  }

            stage("parallel_mvn_build"){
                 parallel{
                   
                   stage("validate:maven"){
                     steps{
                        sh "mvn validate"
                           }
                                  }        
                    
                    stage("compile:maven"){
                      steps{
                         sh "mvn compile"
                           }
                                  }        
                   
                    stage("unit test :maven"){
                      steps{
                         sh "mvn test"
                           }
                                  }
                
                    stage("integration testing:maven"){
                      steps{
                         sh "mvn verify"
                           }
                                  }        
                    
                    stage("package:maven"){
                      steps{
                         sh "mvn package"
                           }
                                  }       
                    
                                       }
    
                                         }  
                                         
                                         
                    stage("maven build"){
                      steps{
                         sh "mvn clean package"
                             }
                                  }
                
                    stage("static code analysis:sonar"){
                      steps{
    
                      withSonarQubeEnv('sonar') {
                      sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=spring-boot-task2 -Dsonar.projectKey=spring-key-task2 -Dsonar.java.binaries=target/classes" 
                      
                                                }
                             }                  
                                  }                
                         
                    stage("docker image build"){
                      steps{
                        sh "sudo docker build -t assign-task2:latest"  
                             }    
                                  }    
                                  
                    stage("docker container"){
                      steps{
                        sh "sudo docker run --name assign-task2-cont -p9000:9000 -d assign-task2:latest"  
                             }    
                                  }  
                            
                    stage("docker login"){
                      steps{
                        sh "export DOCKER_USERNAME=mydoker987 DOCKER_PASSWORD=Thanks@2024 echo $DOCKER_PASSWORD | sudo docker login --username $DOCKER_USERNAME --password-stdin"
                            }
                                 }      
                    
                    stage("docker tag"){
                      steps{
                        sh "sudo docker tag assign-task2:latest mydoker987/assign-task2:latest"  
                            }
                                 }
                                 
                    stage("docker push"){
                      steps{
                        sh "sudo docker push mydoker987/assign-task2:latest "  
                            }
                                 }             


    }

