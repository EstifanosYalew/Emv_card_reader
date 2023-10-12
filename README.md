## EMV NFC Paycard Enrollment ![CI](https://github.com/devnied/EMV-NFC-Paycard-Enrollment/workflows/CI/badge.svg) [![Coverage Status](https://coveralls.io/repos/github/devnied/EMV-NFC-Paycard-Enrollment/badge.svg?branch=master)](https://coveralls.io/github/devnied/EMV-NFC-Paycard-Enrollment?branch=master) [![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.devnied.emvnfccard/library/badge.svg?style=flat)](https://maven-badges.herokuapp.com/maven-central/com.github.devnied.emvnfccard/library) [![License](http://img.shields.io/:license-apache-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0.html)
### Description
Java library used to read and extract public data from NFC EMV credit cards.<br/>
<br/>

First you need to create a custom Provider to exchange APDU with an NFC EMV credit card 
```java
public class YourProvider implements IProvider {

  @Override
  public byte[] transceive(final byte[] pCommand) {
	 // implement this
  }

  @Override
  public byte[] getAt() {
     // implement this to get card ATR (Answer To Reset) or ATS (Answer To Select)
  }

}
```

After that, create an instance of a parser and read the card.
```java
// Create provider
IProvider provider = new YourProvider();
// Define config
Config config = EmvTemplate.Config()
		.setContactLess(true) // Enable contact less reading (default: true)
		.setReadAllAids(true) // Read all aids in card (default: true)
		.setReadTransactions(true) // Read all transactions (default: true)
		.setReadCplc(false) // Read and extract CPCLC data (default: false)
		.setRemoveDefaultParsers(false) // Remove default parsers for GeldKarte and EmvCard (default: false)
		.setReadAt(true) // Read and extract ATR/ATS and description
		; 
// Create Parser
EmvTemplate parser = EmvTemplate.Builder() //
		.setProvider(provider) // Define provider
		.setConfig(config) // Define config
		//.setTerminal(terminal) (optional) you can define a custom terminal implementation to create APDU
		.build();
		
// Read card
EMVCard card = parser.readEmvCard();
```
card object contains all data read (Aid, card number, expiration date, card type, transactions history)

#### Android usage
For android, you can create a provider with [IsoDep](https://developer.android.com/reference/android/nfc/tech/IsoDep) class:
```java
public class Provider implements IProvider {

	private IsoDep mTagCom;

	@Override
	public byte[] transceive(final byte[] pCommand) throws CommunicationException {
		
		byte[] response;
		try {
			// send command to emv card
			response = mTagCom.transceive(pCommand);
		} catch (IOException e) {
			throw new CommunicationException(e.getMessage());
		}

		return response;
	}

	@Override
	public byte[] getAt() {
        // For NFC-A
		return mTagCom.getHistoricalBytes();
		// For NFC-B
        // return mTagCom.getHiLayerResponse();
	}


	public void setmTagCom(final IsoDep mTagCom) {
		this.mTagCom = mTagCom;
	}

}
```



### Maven
```xml
<dependency>
  <groupId>com.github.devnied.emvnfccard</groupId>
  <artifactId>library</artifactId>
  <version>3.0.1</version>
</dependency>
```

### Gradle
```groovy
dependencies {
	compile 'com.github.devnied.emvnfccard:library:3.0.1'
}
```


```xml
mvn clean install
```
## Bugs

Please report bugs and feature requests to the GitHub issue tracker.<br/>
Forks and Pull Requests are also welcome.

