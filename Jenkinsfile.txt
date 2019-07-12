pipeline 
{
agent any
stages {

    stage('Checkout git repo')
	{
		steps 
		{
		  git credentialsId: 'shyamalalakshmi', url: 'https://github.com/ShyamalaLakshmi/JenkinsNunit', branch: 'master'
		}
	}
    
    stage('Build')
	{
		steps
		{
			bat "\"C:/Program Files/dotnet/dotnet.exe\" restore \"${workspace}/PrimeService.sln\""
			bat "\"C:/Program Files/dotnet/dotnet.exe\" build \"${workspace}/PrimeService.sln\""
		}
	}
    
    stage ('UnitTests')
	{
		steps
		{
			bat returnStatus: true, script: "\"C:/Program Files/dotnet/dotnet.exe\" test \"${workspace}/PrimeService.sln\" --logger \"trx;LogFileName=unit_tests.trx\" --no-build"
			
		}
	}
}
	

post 
	{ 
		always 
		{
			 step([$class: 'MSTestPublisher', testResultsPattern: 'PrimeServiceTest\\TestResults\\unit_tests.trx', debug: false, keepJUnitReports: true, skipJUnitArchiver:false, failIfNoResults: true])
		}
	}
}