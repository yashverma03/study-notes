# Intro

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { UserService } from './user.service'; 
import { UserRepository } from './user.repository';
import { NotFoundException } from '@nestjs/common';

describe('UserService', () => {
  let userService: UserService;

  // We create a mock version of UserRepository with only the required methods
  let userRepository: Partial<Record<keyof UserRepository, jest.Mock>>;

  // A mock user object for testing
  const mockUser = { id: 1, name: 'Alice' };

  // 🔧 Runs before each test
  beforeEach(async () => {
    // Mock implementation of UserRepository methods
    userRepository = {
      findOne: jest.fn(),        // mocking findOne method
      createUser: jest.fn(),     // mocking createUser method
    };

    // Create a test module and inject mockUserRepository into it
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        UserService,
        { provide: UserRepository, useValue: userRepository }, // override provider
      ],
    }).compile();

    // Get the instance of UserService from the compiled module
    userService = module.get<UserService>(UserService);
  });

  // 🧹 Runs after each test -- clears mocks
  afterEach(() => {
    jest.clearAllMocks();
  });

  // ✅ getUserById test suite
  describe('getUserById', () => {
    it('should return a user when found', async () => {
      // Arrange: Set up mock to resolve a user
      userRepository.findOne!.mockResolvedValue(mockUser);

      // Act: Call the service method
      const result = await userService.getUserById(1);

      // Assert: Expect the returned value to match the mock
      expect(result).toEqual(mockUser);
      expect(userRepository.findOne).toHaveBeenCalledWith(1);
    });

    it('should throw NotFoundException when user not found', async () => {
      // Arrange: Set up mock to resolve null
      userRepository.findOne!.mockResolvedValue(null);

      // Assert: Expect the service to throw NotFoundException
      await expect(userService.getUserById(999)).rejects.toThrow(NotFoundException);
      expect(userRepository.findOne).toHaveBeenCalledWith(999);
    });
  });

  // ✅ createUser test suite
  describe('createUser', () => {
    it('should create and return a user', async () => {
      // Arrange: Prepare a DTO and expected response
      const dto = { name: 'Bob' };
      const createdUser = { id: 2, name: 'Bob' };

      // Mock the createUser method to resolve with createdUser
      userRepository.createUser!.mockResolvedValue(createdUser);

      // Act: Call the createUser method on the service
      const result = await userService.createUser(dto);

      // Assert: Check that the result matches and mock was called properly
      expect(result).toEqual(createdUser);
      expect(userRepository.createUser).toHaveBeenCalledWith(dto);
    });
  });
});
```

```javascript
describe('Jest Matchers - Reference Suite', () => {
  const numbers = [1, 2, 3];
  const user = { id: 1, name: 'Alice' };
  const string = 'Hello Jest!';

  // ✅ Equality matchers
  it('should match primitive values with toBe', () => {
    expect(5 + 2).toBe(7); // strict equality (===)
  });

  it('should match object structure with toEqual', () => {
    const expected = { id: 1, name: 'Alice' };
    expect(user).toEqual(expected); // deep equality
  });

  // ✅ Truthiness
  it('should validate truthy and falsy values', () => {
    expect(true).toBeTruthy();
    expect('').toBeFalsy();
    expect(null).toBeNull();
    expect(undefined).toBeUndefined();
    expect(5).toBeDefined();
  });

  // ✅ Numbers
  it('should compare numbers correctly', () => {
    expect(10).toBeGreaterThan(5);
    expect(10).toBeGreaterThanOrEqual(10);
    expect(3).toBeLessThan(5);
    expect(3).toBeLessThanOrEqual(3);
    expect(0.1 + 0.2).toBeCloseTo(0.3, 5); // for floating point comparison
  });

  // ✅ Strings
  it('should match strings using regex', () => {
    expect(string).toMatch(/jest/i); // case-insensitive match
    expect(string).toContain('Hello'); // substring check
  });

  // ✅ Arrays
  it('should verify array contents', () => {
    expect(numbers).toContain(2); // check if item is in array
    expect(numbers).toEqual(expect.arrayContaining([1, 3])); // partial match
  });

  // ✅ Exceptions
  it('should check if a function throws error', () => {
    const thrower = () => {
      throw new Error('Boom!');
    };

    expect(thrower).toThrow(); // checks if an error is thrown
    expect(thrower).toThrow('Boom!');
    expect(thrower).toThrow(Error);
  });

  // ✅ Negation
  it('should support negative assertions', () => {
    expect(5).not.toBe(10);
    expect(numbers).not.toContain(99);
  });
});
```
