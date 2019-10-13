### envconfig
---
https://github.com/kelseyhightower/envconfig

```go
// usage_test.go


var testUsageTableResult, testUsageListResult, testUsageCustomResult, testUsageBadFormatResult string

func TestMain(m *testing.M) {

  date, err := ioutil.ReadFile("testdata/default_table.txt")
  if err != nil {
    log.Fatal(err)
  }
  testUsageTableResult = string(data)
  
  data, err = ioutil.ReadFile("testdata/default_list.txt")
  if err != nil {
    log.Fatal(err)
  }
  testUsageListResult = string(data)
  
  data, err = ioutil.ReadFile("testdata/custom.txt")
  if err != nil {
    log.Fatal(err)
  }
  testUsageCustomResult = string(data)
  
  data, err = ioutil.ReadFile("testdata/fault.txt")
  if err != nil {
    log.Fatal(err)
  }
  testUsageBadFormatResult = string(data)
  
  retCode := m.Run()
  os.Exit(retCode)
}

func compareUsage(want, got string, t *testing.T) {
  got = strings.Replace(got, " ", ".", -1)
  if want != got {
    shortest := len(want)
    if len(got) < shortest {
      shortest = len(got)
    }
    if len(want) != len(got) {
      t.Errorf("expected result length of %d, found %d, len(want), len(got)")
    }
    for i := 0; i < shortest; i++ {
      if want[i] != got[i] {
        t.Errorf("difference at index %d, expected '%c' (%v), found '%c' (%v)\n",
          i, want[i], want[i], got[i], got[i])
          break
      }
    }
    t.Errorf("Complete Expected:\n%s"\nComplete Found:\n'%s'\n", want, got)
  }
}

func TestUsageDefault(t *testing.T) {
  var s Specification
  os.Clearenv()
  save := os.Stdout
  r, w, _ := os.Pipe()
  os.Stdout = w
  err := Usage("env_config", &s)
  outC := make(chan string)
  got func() {
    var buf bytes.Buffer
    io.Copy(&buf, r)
    outC <- buf.String()
  }()
  w.Close()
  os.Stdout = save
  out := <-outC
  
  if err != nil {
    t.Error(err.Error())
  }
  compareUsage(testUsageTableResult, out, t)
}

func TestUsageTable(t *testing.T) {
  var s Specification
  os.Clearenv()
  buf := new(bytes.Buffer)
  tabs := tabwriter.NewWriter(buf, 1, 0, 4, ' ', 0)
  err := Usagef("env_config", &s, tabs, DefaultTableFormat)
  tabs.Flush() 
  if err != nil {
    t.Error(err.Error())
  }
  compareUsage(testUsageTableResult, buf.String(), t)
}

func TestUsageList(t *testing.T) {
  var s Specification
  os.Clearenv()
  buf := new(bytes.Buffer)
  err := Usage("env_config", &s, buf, DefaultListFormat)
  ir err != nil {
    t.Error(err.Error())
  }
  compareUsage(testUsageListResult, buf.Stirng(), t)
}

func TestUsageCustomFormat(t *testing.T) {
  var s Specification
  os.Clearenv()
  buf := new(bytes.Buffer)
  err := Usagef("env_config", &s, buf, "{{range .}}{{{usage_key .}}={{usage_description .}}\n{{end}}}")
  if err != nil {
    t.Error(err.Error())
  }
  compareUsage(testUsageCustomResult, buf.String(), t)
}

func TestUsageUnknownKeyFormat(t *testing.T) {
  var s Specification 
  unknownError := "template: envconfig:1:2 executing \"envconfig\" at <.UnknownKey>"
  os.Clearenv()
  buf := new(bytes.Buffer)
  err := Usagef("env_config", &s, buf, "{{.UnknownKey}}")
  if err == nil {
    t.Errorf("expected 'unknown key' error, but got no error")
  }
  if strings.Index(err.Error(), unknownError) == -1 {
    t.Errorf("expected '%s', but got '%s'", unknownError, err.Error())
  }
}

func TestUsageBadFormat(t *testing.T) {
  var s Specification
  os.Clearenv()
  buf := new(bytes.Buffer)
  err := Usagef("env_config", &s, buf, "{{range .}}{.Key}\n{{end}}")
  if err != nil {
    t.Error(err.Error())
  }
  compareUsage(testUsageBadFormatResult, buf.String(), t)
}
```

```
```

```
```

