# Serverless Testing Practices For AWS Lambdas

This repository is a part of a M.Sc. thesis work under the CloWeE research group at Tampere University. It is a fork of a sample application created and maintained by the Serverless Framework. The purpose of this repository is to showcase the application of different automatic testing approaches for AWS Lambda applications.

The application has been modified in the following ways from the original fork:
- Converted to "traditional" Serverless Framework configuration (using AWS CloudFormation) away from Serverless Components
- Converted the API application from JavaScript to TypeScript
  - Added a few null/undefined checks to fix TS errors
  - Small improvements like using object destructuring in some places (when accessing environment variables)

The application now also features a Pulumi Infrastructure-as-Code (IaC) program for more automated deployments (under api/infrastructure directory). Tests were implemented using the Jest testing framework for unit and integration tests, and Cypress.io for E2E tests.

## Applied Testing Approaches

The repository showcases unit, integration, and system (E2E) testing applied to a full-stack application as a part of the thesis regarding this repository. The thesis studies solutions for the documented difficulties found in current scientific publications. The thesis identified three integration testing approaches by conducting a literature review on guidebooks for serverless application development; local, integration, and cloud integration testing. The Pulumi IaC application is used to overcome some difficulties regarding testing with real-world AWS cloud infrastructure. 

The tests can be found in the following directories within this repository:

| Testing approach   | Directory                    |
| ------------------ | ---------------------------- |
| Unit               | api/tests/unit               |
| Local integration  | api/tests/integration/local  |
| Hybrid integration | api/tests/integration/hybrid |
| Cloud integration  | api/tests/cloud              |
| E2E system         | api/tests/e2e                |

Additionally, the Cypress configuration is located under api/cypress directory and Jest configuration is located in the api/jest.config.js file.

## Developed test cases
The original application does not have any pre-existing tests in its original Git fork. Therefore, there were not any existing dependencies regarding testing frameworks and other tooling choices.
Therefore, we developed a complete test-suite from scratch.  The general scope of the tests is the back end serverless function, with end-to-end tests extending to the entire full-stack application. The Pulumi application and the front end application were not tested separately.

The unit test suites reached a total coverage of 100\% in all areas for the testable units. The integration test cases reached a statement coverage of 93.33\%, a branch coverage of 74.07\%, a function coverage of 100\%, and a line coverage of 100\%. The combined coverage is 100\% in all areas; refer to Appendix A for complete coverage tables.


### Unit Test
Please, add Table 5.1 here

### Integration Test
Please, add Table 5.2 here


## Instructions for Testing

The instructions in this file shows how to deploy the application using Pulumi and how to run the test suites. For the original instructions displaying how to deploy the application using the Serverless Framework, refer to the [original README file](README.original.md). The original process using Serverless Framework is mostly the same despite moving away from the original component implementation, though now it is only necessary to deploy the API project.

To run the program as is you will need to install [Node.js](https://nodejs.org/en/). If you wish to run cloud tests you will also need [Serverless Framework](https://www.npmjs.com/package/serverless), [AWS CLI](https://aws.amazon.com/cli/), and [Pulumi](https://www.pulumi.com/docs/get-started/aws/begin/), along with AWS and Pulumi accounts.

Before moving on, you will also need to install node modules for the project using the ```npm i``` command inside the api and site directories.

## Pulumi

Pulumi deployment can be ran manually with the following commands:
```
# If you leave out the --stack flags, Pulumi will either prompt you to select a stack or default to the previously used one.
# Deploy stack (cloud resources)
pulumi up --stack $STACK
# Destroy resources associated with the stack
pulumi destroy --stack $STACK
# Remove stack
pulumi stack rm $STACK
```

## Jest

To run all tests with Jest you can use the following command:
```npx jest```

However, as both cloud and hybrid tests deploy separate cloud environments, you might want to run tests separately. For this you can utilize regex patterns along with the Jest commands: 
```
npx jest \"tests/integration/local\|tests/unit\"
npx jest tests/integration/hybrid
npx jest tests/integration/cloud
```
If you wish to ignore the expected console prints from code, you can also pass the flag ```--silent ```to Jest.

The hybrid tests are set up to only deploy a DynamoDB instance to AWS, whereas the cloud tests will deploy the entire API excluding the front end. This means that the hybrid tests are significantly faster to run. The local tests run the fastest, as they do not require any external services.

## Cypress

To run the tests with Cypress, use the command ```npx cypress run```. You can also execute tests using the Cypress GUI using ```npx cypress open```. It's worth noting that the Cypress tests are slow to run, as especially the CloudFront deployment takes time. Also note, that contrary to the documentation, the Cypress GUI doesn't currently seem to correctly run the teardown hook for the Pulumi stack, and has to be done manually afterwards.

If you wish to deploy the environments separately beforehand, you can manually pass the CloudFront distribution endpoint to Cypress using an environment variable called ```URL``` while disabling Pulumi deployment, e.g. ```npx cypress run --env SKIP_PULUMI=true,URL=$DISTRIBUTION_URL```.
