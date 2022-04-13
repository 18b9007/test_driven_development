*The cloned repository will be posted once all the new TDD related code have been added into the project
# Test Driven Development (In progress)
![red-green-refactor](https://user-images.githubusercontent.com/98511395/162567636-658baefd-0d84-45d8-98d8-f8ab0dbf0e62.png)

TDD is simply the iteration of short development cycles where you:
1. Write a test that is expected to fail
2. Write an implementation code that makes the test pass
3. Refactor the code without changing how it behaves

### [TDD Tutorial by Amigoscode](https://www.youtube.com/watch?v=z6gOPonp2t0)
#### Setting up the repository
1. Cloned the provided [repository](https://github.com/amigoscode/software-testing) as instructed in the tutorial
2. Checkout to specific [branch](https://github.com/amigoscode/software-testing/tree/6-integration-testing) to practice TDD
3. In the **test** folder, created **utils** package containing new empty test class **PhoneNumberValidatorTest**. This will be used for the existing **CustomerRegistrationService** class found in the **main** folder. What they do: Validate that phone number is valid

#### TDD cycle 1
Test code: Empty PhoneNumberValidatorTest class
<br>How it failed: PhoneNumberValidator class doesn't exist

```Java
package com.amigoscode.testing.utils;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

class PhoneNumberValidatorTest {
    
    private PhoneNumberValidator underTest;
    @BeforeEach

    
    void setUp()
    { underTest = new PhoneNumberValidator(); }
    
    @Test
    void itShouldValidatePhoneNumber(){
        // Given
        // When
        // Then
    }
}
```
Implementation Code: Created the required PhoneNumberValidator class. The test now passes but doesn't actually do anything
<br>Refactor: It should be able to validate phone number (next cycle)

```Java
package com.amigoscode.testing.utils;

public class PhoneNumberValidator {
    
}
```

#### TDD Cycle 2
Test code: Functionality to PhoneNumberValidatorTest
<br>How it failed: validate() function does not exist 

```Java
package com.amigoscode.testing.utils;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

class PhoneNumberValidatorTest {
    
    private PhoneNumberValidator underTest;
    @BeforeEach
    
    void setUp()
    { underTest = new PhoneNumberValidator(); }
    
    @Test
    void itShouldValidatePhoneNumber(){
        // Given ... a phone number
        String phoneNumber = "+4470000000";
        // When
        boolean isValid = underTest.validate(phoneNumber);
        // Then
    }
}
```

Implementation code: Created the required function, naming it test() instead of validate().
<br>The test can run but it will fail, showing that the expected is True but the actual was False (because we wrote it to be False)
<br>Refactor: Implement logic to PhoneValidator (next cycle)

refactor: implement logic to phonevalidator

```Java
package com.amigoscode.testing.utils;
import java.util.function.Predicate;

public class PhoneNumberValidator implements Predicate <String> {

    @Override
	public boolean test(String s){
		return false;
    }
}
```

```Java
package com.amigoscode.testing.utils;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.*;

class PhoneNumberValidatorTest {
    
    private PhoneNumberValidator underTest;
    @BeforeEach
    
    void setUp()
    { underTest = new PhoneNumberValidator(); }
    
    @Test
    void itShouldValidatePhoneNumber(){
        // Given ... a phone number
	String phoneNumber = "+4470000000";
        // When
	boolean isValid = underTest.test(phoneNumber);
        // Then
	assertThat(isValid).isTrue();
    }
}
```

#### TDD Cycle 3
Test: validation logic
<br>How it failed: It could run. but only because the test script value was already in the correct format. It doesn't validate alot of other things

```Java
package com.amigoscode.testing.utils;
import java.util.function.Predicate;

public class PhoneNumberValidator implements Predicate <String> {
    @Override
	public boolean test(String phoneNumber){
	return phoneNumber.startsWith("+44") &&
	phoneNumber.length() == 13;
    }
}
```

Implementation code: To address all possible scenarios: correct, wrong length, doesnt start with +
<br>Refactor: Condense all the tests set up into a single test that passes parameters, i.e. Parameterized Test (next cycle)

```Java
package com.amigoscode.testing.utils;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.*;

class PhoneNumberValidatorTest {
    
    private PhoneNumberValidator underTest;
    @BeforeEach
    
    void setUp()
    { underTest = new PhoneNumberValidator(); }
    
    @Test
    void itShouldValidatePhoneNumber(){
        // Given ... a phone number
	String phoneNumber = "+447000000000";
        // When
	boolean isValid = underTest.test(phoneNumber);
        // Then
        assertThat(isValid).isTrue();
    }
    
    @Test
    @DisplayName("Should fail when length > 13")
    void itShouldValidatePhoneNumberWhenIncorrectAndLengthBiggerThan13(){
        // Given ... a phone number
        String phoneNumber = "+4470000007890";
        // When
        boolean isValid = underTest.test(phoneNumber);
        // Then
        assertThat(isValid).isFalse();
    }
    
    @Test
    @DisplayName("Should fail when no start with +")
    void itShouldValidatePhoneNumberNoStartWithPlusSign(){
        // Given ... a phone number
        String phoneNumber = "4470000000";
        // When
        boolean isValid = underTest.test(phoneNumber);
        // Then
        assertThat(isValid).isFalse();
    }
}
```

#### TDD Cycle 4
Test code: Parameterized for ONE of the tests
<br>How it failed: n/a

```Java
package com.amigoscode.testing.utils;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import static org.assertj.core.api.Assertions.*;

class PhoneNumberValidatorTest {
    
    private PhoneNumberValidator underTest;
    @BeforeEach
    
    void setUp()
    { underTest = new PhoneNumberValidator(); }
    
    @ParameterizedTest
    @CsvSource({"+447000000000, true"})
    void itShouldValidatePhoneNumber(String phoneNumber, boolean expected){
        // When
        boolean isValid = underTest.test(phoneNumber);
        // Then
        assertThat(isValid).isEqualTo(expected);
    }
    
    @Test
    @DisplayName("Should fail when length > 13")
    void itShouldValidatePhoneNumberWhenIncorrectAndLengthBiggerThan13(){
        // Given ... a phone number
        String phoneNumber = "+4470000007890";
        // When
        boolean isValid = underTest.test(phoneNumber);
        // Then
        assertThat(isValid).isFalse();
    }
    
    @Test
    @DisplayName("Should fail when no start with +")
    void itShouldValidatePhoneNumberNoStartWithPlusSign(){
        // Given ... a phone number
        String phoneNumber = "4470000000";
        // When
        boolean isValid = underTest.test(phoneNumber);
        // Then
        assertThat(isValid).isFalse();
    }
}
```
Implementation code: Parameterized for all 3 tests. Resultant test code is shorter and cleaner
<br>Refactor (but is it really): introduce this to the actual CustomerRegistrationService.java, which was the original code which had the TODO to validate phone number. (next cycle)

```Java
package com.amigoscode.testing.utils;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import static org.assertj.core.api.Assertions.*;

class PhoneNumberValidatorTest {
    
    private PhoneNumberValidator underTest;
    @BeforeEach
    
    void setUp()
    { underTest = new PhoneNumberValidator(); }
    
    @ParameterizedTest
    @CsvSource({
        "+447000000000, true",
        "+4470000007890, false",
        "4470000000, false"
})
    void itShouldValidatePhoneNumber(String phoneNumber, boolean expected){
        // When
        boolean isValid = underTest.test(phoneNumber);
        // Then
        assertThat(isValid).isEqualTo(expected);
    }
}
```

#### TDD Cycle 5
Updated CustomerRegistrationService.java

```Java
package com.amigoscode.testing.customer;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.Optional;
import java.util.UUID;

import com.amigoscode.testing.utils.PhoneNumberValidator;

@Service
public class CustomerRegistrationService {

    private final CustomerRepository customerRepository;
    private final PhoneNumberValidator phoneNumberValidator;

    @Autowired
    public CustomerRegistrationService(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
        this.phoneNumberValidator = phoneNumberValidator;
    }

    public void registerNewCustomer(CustomerRegistrationRequest request) {
        String phoneNumber = request.getCustomer().getPhoneNumber();

        // TODO: Validate that phone number is valid
        if (!phoneNumberValidator.test(phoneNumber))
        { throw new IllegalStateException("Phone Number " + phoneNumber + " is not valid"); }

        Optional<Customer> customerOptional = customerRepository
                .selectCustomerByPhoneNumber(phoneNumber);

        if (customerOptional.isPresent()) {
            Customer customer = customerOptional.get();
            if (customer.getName().equals(request.getCustomer().getName())) {
                return;
            }
            throw new IllegalStateException(String.format("phone number [%s] is taken", phoneNumber));
        }

        if(request.getCustomer().getId() == null) {
            request.getCustomer().setId(UUID.randomUUID());
        }

        customerRepository.save(request.getCustomer());
    }
}
```

Then added test code to CustomerRegistrationServiceTest.java, failed as expected, so we fix those failed tests

```Java
package com.amigoscode.testing.customer;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.Optional;
import java.util.UUID;

import com.amigoscode.testing.utils.PhoneNumberValidator;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;
import static org.mockito.BDDMockito.then;
import static org.mockito.Mockito.never;

class CustomerRegistrationServiceTest {

    @Mock
    private CustomerRepository customerRepository;

    // Newly added mock
    @Mock
    private PhoneNumberValidator phoneNumberValidator;

    @Captor
    private ArgumentCaptor<Customer> customerArgumentCaptor;

    private CustomerRegistrationService underTest;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
        underTest = new CustomerRegistrationService(customerRepository);
    }

    @Test
    void itShouldSaveNewCustomer() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(UUID.randomUUID(), "Maryam", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);

        // ... No customer with phone number passed
        given(customerRepository.selectCustomerByPhoneNumber(phoneNumber))
                .willReturn(Optional.empty());

        // When
        underTest.registerNewCustomer(request);

        // Then
        then(customerRepository).should().save(customerArgumentCaptor.capture());
        Customer customerArgumentCaptorValue = customerArgumentCaptor.getValue();
        assertThat(customerArgumentCaptorValue).isEqualTo(customer);
    }

    @Test
    void itShouldSaveNewCustomerWhenIdIsNull() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(null, "Maryam", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);

        // ... No customer with phone number passed
        given(customerRepository.selectCustomerByPhoneNumber(phoneNumber))
                .willReturn(Optional.empty());

        // When
        underTest.registerNewCustomer(request);

        // Then
        then(customerRepository).should().save(customerArgumentCaptor.capture());
        Customer customerArgumentCaptorValue = customerArgumentCaptor.getValue();
        assertThat(customerArgumentCaptorValue)
                .isEqualToIgnoringGivenFields(customer, "id");
        assertThat(customerArgumentCaptorValue.getId()).isNotNull();
    }

    @Test
    void itShouldNotSaveCustomerWhenCustomerExists() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(UUID.randomUUID(), "Maryam", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);

        // ... an existing customer is retuned
        given(customerRepository.selectCustomerByPhoneNumber(phoneNumber))
                .willReturn(Optional.of(customer));

        // When
        underTest.registerNewCustomer(request);

        // Then
        then(customerRepository).should(never()).save(any());
    }

    @Test
    void itShouldThrowWhenPhoneNumberIsTaken() {
        // Given a phone number and a customer
        String phoneNumber = "000099";
        Customer customer = new Customer(UUID.randomUUID(), "Maryam", phoneNumber);
        Customer customerTwo = new Customer(UUID.randomUUID(), "John", phoneNumber);

        // ... a request
        CustomerRegistrationRequest request = new CustomerRegistrationRequest(customer);

        // ... No customer with phone number passed
        given(customerRepository.selectCustomerByPhoneNumber(phoneNumber))
                .willReturn(Optional.of(customerTwo));

        // When
        // Then
        assertThatThrownBy(() -> underTest.registerNewCustomer(request))
                .isInstanceOf(IllegalStateException.class)
                .hasMessageContaining(String.format("phone number [%s] is taken", phoneNumber));

        // Finally
        then(customerRepository).should(never()).save(any(Customer.class));

    }
}
```

Implementation code: Fixing the 4 failed tests

```Java

```
