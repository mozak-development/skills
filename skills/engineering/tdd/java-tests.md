# Good and Bad Tests

## Good Tests

**Integration-style**: Test through real interfaces, not mocks of internal parts.

```java
// GOOD: Tests observable behavior
@Test  
@DisplayName("Missing initial captures can make the start gap longer than one minute")  
void missingInitialCaptures() {  
    givenSnapshots(List.of(snapshot(180, true, 180), snapshot(3600, true, 3600)));  
  
    assertThat(breakdown(true).getAvailabilityPlannedLossPieces()).isEqualTo(-180);  
    assertThat(breakdown(false).getAvailabilityPlannedLossPieces()).isZero();  
}
```

Characteristics:

- Tests behavior users/callers care about
- Uses public API only
- Survives internal refactors
- Describes WHAT, not HOW
- One logical assertion per test
- use `@DisplayName` to describe the intention of the test. Start with a short sentence. Second sentence can extend the description
- if the intension is more complex, use javadoc comments for the test to describe it more clearly
- use inline comments sparly to illustrate the setup steps and explain assertions if they are not trivial
- use `@ParameterizedTest` to test variations of input data
- 

## Bad Tests

**Implementation-detail tests**: Coupled to internal structure.

```java
// BAD: Tests implementation details
@Test  
void processOneRowAndCallExternalService_shouldUpdateProductionModeBeforePersistingMetric() {  
    when(namedParameterJdbcTemplate.queryForList(anyString(), any(org.springframework.jdbc.core.namedparam.MapSqlParameterSource.class)))  
        .thenReturn(List.of(Map.of(  
            "time", "ignored",  
            "mod_general_timestamp", "ignored",  
            "packml_mode", 1,  
            "packml_state", 6,  
            "sensor", 42  
        )));  
  
    ReflectionTestUtils.setField(cut, "dataRetrievalInitialized", true);  
    ReflectionTestUtils.setField(cut, "rowCountPerModule", new java.util.HashMap<>(Map.of("mod1", 1)));  
  
    cut.processOneRowAndCallExternalService();  
  
    InOrder inOrder = inOrder(productionModeStateUpdater, rawDataConsumer);  
    inOrder.verify(productionModeStateUpdater).updateProductionModeState(any(), any());  
    inOrder.verify(rawDataConsumer).consumeMetric(any());  
}
```

Red flags:

- Bypasses initialization by modifying private fields through reflection.
- Uses any() throughout verification, so incorrect metric/state contents could still pass.
- Mocking internal collaborators
- Testing private methods
- Asserting on call counts/order
- Test breaks when refactoring without behavior change
- Test name describes HOW not WHAT
- Verifying through external means instead of interface

```java
// BAD: It calls the public interface but verifies an implementation method on a spy of the class under test. It never checks result.
 @Test
   void shouldLoadTranslationsWhenNotInCache() throws IOException {
       String value = "@HMI123@ - @HMI456@";
       String defaultValue = "default";
       String moduleName = "module1";
       Locale i18n = Locale.forLanguageTag("en-GB");

       String content = "HMI123|Opening\nHMI456|bottle";
       mockResourceWithContent(content);

       String result = translationService.getMessage(value, defaultValue, moduleName, i18n);

       verify(translationService, times(1)).loadTranslationFile(moduleName, i18n);
   }

// GOOD: Verifies through interface
 @Test
   void shouldReturnTranslationWhenCacheIsEmpty() throws IOException {
       String value = "@HMI123@ - @HMI456@";
       String defaultValue = "default";
       String moduleName = "module1";
       Locale i18n = Locale.forLanguageTag("en-GB");

       Translations translations = new Translations();
       translations.addItem("HMI123", "Opening");
       translations.addItem("HMI456", "bottle");

       String content = "HMI123|Opening\nHMI456|bottle";
       mockResourceWithContent(content);

       String result = translationService.getMessage(value, defaultValue, moduleName, i18n);

       assertEquals("Opening - bottle", result);
   }
```

**Tautological tests**: Expected value restates the implementation, so the test passes by construction.

```java
// BAD: Expected value is recomputed the way the code computes it
@Test  
void findResources() throws IOException {  
  
    ExcelConfigurationResourceLocator locator = new ExcelConfigurationResourceLocator(resourceLoader, "classpath:/*.xlsx");  
    assertNotNull(locator);  
  
    PathMatchingResourcePatternResolver matcher = new PathMatchingResourcePatternResolver(resourceLoader);  
    int expectedCount = matcher.getResources("classpath:/*.xlsx").length;  
  
    assertThat(locator.locateResources()).hasSize(expectedCount);  
}

// with the implementation

@Override  
public Set<String> locateResources() {  
    try {  
  
        PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver(resourceLoader);  
        Resource[] resources = resolver.getResources(filePattern);  
  
        if (resources.length == 0 || ObjectUtils.isEmpty(filePattern)) {  
            throw new ConfigException("No Excel configuration resources found for pattern: '" + filePattern + "'");  
        }  
        return Arrays.stream(resources)  
                .map(this::getResourcePath)  
                .collect(Collectors.toSet());  
    } catch (IOException e) {  
        // Log the error and return an empty set  
        throw new ConfigException("Excel Config Resources couldn't be located for pattern " + filePattern, e);  
    }  
}

//  The expected count repeats the implementation’s resolver call with the same pattern // and loader, rather than using independently known fixtures. Both sides can therefore // agree on an incorrect discovery result.

```

Better approach would be:

Use known files as fixtures and assert their exact paths, rather than invoking the resolver again to calculate expectations

```java
// GOOD: Expected value is an independent, known literal
@Test
void findsOnlyExcelFiles() throws IOException {
   Path machine = Files.createFile(directory.resolve("machine.xlsx"));
   Path parameters = Files.createFile(directory.resolve("parameters.xlsx"));
   Files.createFile(directory.resolve("notes.txt"));

   var locator = new ExcelConfigurationResourceLocator(
		   new DefaultResourceLoader(),
		   directory.toUri() + "*.xlsx");

   assertThat(locator.locateResources()).containsExactlyInAnyOrder(
		   machine.toUri().toURL().toString(),
		   parameters.toUri().toURL().toString());
}
```