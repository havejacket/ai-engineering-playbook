# Unit Test Generator Prompt Library

## Purpose

This prompt template helps generate comprehensive unit tests for functions and components based on specifications. It's designed to create tests that verify functionality, handle edge cases, and use appropriate mocking techniques.

## Base Prompt Template

```
Generate unit tests for the following {language} function/component:

{function_code_or_spec}

Requirements for the tests:
- Testing framework: {framework} (e.g., Jest, pytest, JUnit)
- Coverage: Test the main functionality and these edge cases: {edge_cases}
- Dependencies to mock: {dependencies}
- Any specific assertions or validation techniques: {assertion_requirements}

Please include:
- Complete test setup with necessary imports
- Descriptive test names that explain what is being tested
- Appropriate mocks/stubs for external dependencies
- Clear assertions that validate expected outcomes
- Comments explaining the purpose of complex test scenarios
```

## Example: JavaScript API Function Test

```
Generate unit tests for the following JavaScript function:

```javascript
// user-service.js
const axios = require('axios');

async function getUserData(userId) {
  if (!userId) {
    throw new Error('User ID is required');
  }
  
  try {
    const response = await axios.get(`https://api.example.com/users/${userId}`);
    
    if (response.status === 200) {
      const userData = response.data;
      return {
        id: userData.id,
        name: userData.name,
        email: userData.email,
        isActive: userData.status === 'active'
      };
    } else {
      throw new Error(`Failed to fetch user: ${response.statusText}`);
    }
  } catch (error) {
    if (error.response && error.response.status === 404) {
      return null; // User not found
    }
    throw new Error(`Error fetching user data: ${error.message}`);
  }
}

module.exports = { getUserData };
```

Requirements for the tests:
- Testing framework: Jest
- Coverage: Test successful retrieval, user not found (404), other API errors, and invalid input
- Dependencies to mock: axios
- Assertions: Verify the returned object structure and properties for success case, null for not found case, and error throwing for other cases

Please include:
- Complete test setup with necessary imports and Jest configuration if needed
- Descriptive test names that explain what is being tested
- Appropriate mocks for axios
- Clear assertions that validate expected outcomes
- Comments explaining the purpose of complex test scenarios
```

## Example Output (JavaScript/Jest)

```javascript
// user-service.test.js
const axios = require('axios');
const { getUserData } = require('./user-service');

// Mock axios module
jest.mock('axios');

describe('getUserData', () => {
  // Reset mocks before each test
  beforeEach(() => {
    jest.clearAllMocks();
  });

  test('should return formatted user data when API call is successful', async () => {
    // Arrange: Mock successful API response
    const mockUserData = {
      id: '123',
      name: 'John Doe',
      email: 'john@example.com',
      status: 'active'
    };
    
    axios.get.mockResolvedValueOnce({
      status: 200,
      data: mockUserData
    });

    // Act: Call the function with a user ID
    const result = await getUserData('123');

    // Assert: Verify the API was called correctly
    expect(axios.get).toHaveBeenCalledWith('https://api.example.com/users/123');
    
    // Assert: Verify the returned data is formatted correctly
    expect(result).toEqual({
      id: '123',
      name: 'John Doe',
      email: 'john@example.com',
      isActive: true
    });
  });

  test('should return null when user is not found (404)', async () => {
    // Arrange: Mock 404 response
    const error = new Error('User not found');
    error.response = { status: 404 };
    axios.get.mockRejectedValueOnce(error);

    // Act: Call the function with a non-existent user ID
    const result = await getUserData('nonexistent');

    // Assert: Verify null is returned for non-existent users
    expect(result).toBeNull();
  });

  test('should throw an error for other API errors', async () => {
    // Arrange: Mock a 500 server error
    const error = new Error('Server error');
    error.response = { status: 500 };
    axios.get.mockRejectedValueOnce(error);

    // Act & Assert: Verify the function throws an error
    await expect(getUserData('123')).rejects.toThrow('Error fetching user data');
  });

  test('should throw an error when no userId is provided', async () => {
    // Act & Assert: Verify the function validates input
    await expect(getUserData()).rejects.toThrow('User ID is required');
    await expect(getUserData(null)).rejects.toThrow('User ID is required');
    await expect(getUserData('')).rejects.toThrow('User ID is required');
  });
});
```

## Best Practices

1. **Test Isolation**: Each test should be independent and not rely on the state from other tests.
2. **Descriptive Test Names**: Use clear, descriptive names that explain what functionality is being tested.
3. **Arrange-Act-Assert Pattern**: Structure tests with clear setup, action, and verification phases.
4. **Mock External Dependencies**: Isolate the code being tested by mocking external services, APIs, and databases.
5. **Test Edge Cases**: Include tests for boundary conditions, error scenarios, and unexpected inputs.
6. **Avoid Test Duplication**: Use parameterized tests or test helpers for similar test cases.
7. **Readable Assertions**: Make assertions clear and specific about what is being verified.
8. **Coverage Considerations**: Aim for high test coverage, but focus on critical paths and complex logic.
