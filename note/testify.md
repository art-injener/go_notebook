```go
package data

import (
	"log"
	"testing"

	"github.com/stretchr/testify/suite"
)

type MyTestSuite struct {
	suite.Suite
	db *Store
}

// listen for 'go test' command --> run test methods
func TestMyTestSuite(t *testing.T) {
	suite.Run(t, new(MyTestSuite))
}

// run once, before test suite methods
func (s *MyTestSuite) SetupSuite() {
	log.Println("SetupSuite()")
	// connect the database, save to 's.db'
}

// run once, after test suite methods
func (s *MyTestSuite) TearDownSuite() {
	log.Println("TearDownSuite()")
	// delete the created database
}

// run before each test
func (s *MyTestSuite) SetupTest() {
	log.Println("SetupTest()")
}

// run after each test
func (s *MyTestSuite) TearDownTest() {
	log.Println("TearDownTest()")
}

// run before each test
func (s *MyTestSuite) BeforeTest(suiteName, testName string) {
	log.Println("BeforeTest()", suiteName, testName)
}

// run after each test
func (s *MyTestSuite) AfterTest(suiteName, testName string) {
	log.Println("AfterTest()", suiteName, testName)
}

func (s *MyTestSuite) TestExample1() {
	s.Equal(true, true)
}

func (s *MyTestSuite) TestExample2() {
	s.Equal(true, true)
}
```