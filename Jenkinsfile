pipeline {
  agent any
  stages {
    stage('CheckParametes') {
      when {
        expression {
          params.Is_PGP_Encrypted_File == false
        }

      }
      steps {
        echo "Source Server ${params.Source_Server}"
        echo "Source File Location: ${params.Source_File_Location}"
        echo "Source File Name: ${params.Source_File_Name}"
        echo "Destination Server ${params.Destination_Server}"
        echo "Destination File Location: ${params.Destination_File_Location}"
        echo "Destination File Name: ${params.Destination_File_Name}"
        echo "Target File Ownership: ${params.Target_File_Ownership}"
        echo "Target File Permissions - Full ?: ${params.Is_Target_File_Permissions_Full}"
      }
    }

    stage('Test-SSH') {
      post {
        success {
          echo 'Able to connect to DDE servers over SSH.'
        }

      }
      steps {
        echo 'Testing SSH Connectivity..'
        sh 'ssh -o StrictHostKeyChecking=no user@server1 uptime'
        sh 'ssh -o StrictHostKeyChecking=no user@server2 uptime'
      }
    }

    stage('Pull-Files') {
      post {
        success {
          echo 'Able to connect to DDE servers over SSH.'
        }

      }
      steps {
        echo 'Pulling Files from Source..'
        sh ssh -q --batch-mode user@${params.Source_Server} "dzdo /opt/envmgr/bin/pull_files_source -l ${params.Source_File_Location} -f ${params.Source_File_Name} -e ${params.Is_PGP_Encrypted_File}"
        sh scp -q --batch-mode user@${params.Source_Server}:/tmp/dde_file_copy/${params.Source_File_Name} .
      }
    }

  }
  parameters {
    booleanParam(name: 'Is_PGP_Encrypted_File', defaultValue: false, description: 'Is your file PGP encrypted')
    choice(name: 'Source_Server', choices: '''server1
server2''', description: 'Select the source server from the list')
    string(name: 'Source_File_Location', defaultValue: '/tmp/', description: 'Provide Source File Location')
    string(name: 'Source_File_Name', defaultValue: 'SampleFileName.dat', description: 'Provide Source File Name')
    choice(name: 'Destination_Server', choices: 'server2', description: 'Select the Destination server from the list')
    string(name: 'Destination_File_Location', defaultValue: '/tmp/', description: 'Provide Destination File Location')
    string(name: 'Destination_File_Name', defaultValue: 'SampleFileName.dat', description: 'Provide Destination File Name')
    choice(name: 'Target_File_Ownership', choices: '''ddebatch
degbatch
ddebat01
ddebat02
ddebat03
ddebat04''', description: 'Who should own the file in target location ?')
    booleanParam(name: 'Is_Target_File_Permissions_Full', defaultValue: false, description: 'Check above box to enable full permissions on target file ?')
  }
}
