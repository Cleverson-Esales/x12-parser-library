# X12 Parser / Serializer

### Releases

##### 1.0.4
- Made some improvements on the parsing process to make the segment information reading easier
  Instead of "$GS->GS06" now you can make "$GS->GroupControlNumber"


### Including this library

You'll need to include this library in your project in order to use it. First, add the following to the repositories section of your composer.json file:
```json
{
  ...
  "repositories": [
    {
      "type": "vcs",
      "url":  "ssh://git@git.uhin.org/shr/x12-parser.git"
    }
  ]
}
```

You can now include the library into your project by running:

```bash
composer require uhin/x12-parser:1.0.4
```

Once you're included the library in your project, you can now begin writing code to parse and serialize X12 EDI.


### Examples

##### Parsing a file
```php
use Uhin\X12Parser\Parser\X12Parser;

// Load the file into memory
$rawX12 = file_get_contents('path-to-your-file');

// Create a parser object
$parser = new X12Parser($rawX12);

// Parse the file into an object data structure
$x12 = $parser->parse();
$x12 = $parser->parse(true); //Activate the logging
// $x12 is now an X12 object containing all of the information from the X12 file
```

##### Accessing/Modifying data in the X12
Note: If any segment/property can have multiples then it will always be parsed into an array, even if there is only one present. 
This is to avoid confusion on whether or not you are dealing with an array or an object.
```php
// Using the $x12 object from the example above...

// Retrieving the GS06
$gs06 = $x12->ISA[0]->GS[0]->GS06;
$gs06 = $x12->ISA[0]->GS[0]->GroupControlNumber; //You can use the element name

// Setting the GS06 value
$x12->ISA[0]->GS[0]->GS06 = 'Changed Value';
$x12->ISA[0]->GS[0]->GroupControlNumber = 'Changed Value';
```

##### Converting a parsed file back to X12
```php
use Uhin\X12Parser\Serializer\X12Serializer;

// Using the $x12 object from the example above...
$serializer = new X12Serializer($x12);

// Options for serializing:
$serializer->addNewLineAfterSegment(true);
$serializer->addNewLineAfterIEA(true);

// Generate the raw X12 string
$rawX12 = $serializer->serialize();
```


### Unit Tests

First, make sure that you've placed your test files in the `tests/test-files` directory.

__NOTE:__ _Don't commit these files to git if they have any HIPPA data._ 

The current tests will look for the following files in the `tests/test-files` directory:
- `277.txt`
- `835.txt`
- `837.txt`
- `999.txt`
- `TA1.txt`

Once you have your files in place, you can run this command from the root of the library:

```bash
./vendor/bin/phpunit --bootstrap ./vendor/autoload.php --testdox tests
```