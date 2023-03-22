```
import (
	"context"
	"fmt"
	"github.com/aws/aws-sdk-go-v2/aws"
	"github.com/aws/aws-sdk-go-v2/config"
	"github.com/aws/aws-sdk-go-v2/service/lambda"
	"github.com/aws/aws-sdk-go-v2/service/lambda/types"
)
```

```
func invokeLambda(functionName string, payload []byte) ([]byte, error) {
	// Load the SDK's configuration
	cfg, err := config.LoadDefaultConfig(context.Background())
	if err != nil {
		return nil, fmt.Errorf("unable to load SDK config: %v", err)
	}

	// Create a Lambda client
	client := lambda.NewFromConfig(cfg)

	// Set up the input for the Invoke operation
	input := &lambda.InvokeInput{
		FunctionName:   aws.String(functionName),
		InvocationType: types.InvocationTypeRequestResponse,
		Payload:        payload,
	}

	// Call the Lambda function
	resp, err := client.Invoke(context.Background(), input)
	if err != nil {
		return nil, fmt.Errorf("failed to invoke Lambda: %v", err)
	}

	// Check if Lambda returned an error
	if resp.FunctionError != nil {
		return nil, fmt.Errorf("Lambda error: %s", *resp.FunctionError)
	}

	// Return the response payload
	return resp.Payload, nil
}
```

```
functionName := "your-lambda-function-name"
	payload := []byte(`{"key": "value"}`) // JSON payload to pass to your Lambda function

	responsePayload, err := invokeLambda(functionName, payload)
	if err != nil {
		fmt.Printf("Error invoking Lambda: %v\n", err)
		return
	}

	fmt.Printf("Lambda response: %s\n", string(responsePayload))
  ```
