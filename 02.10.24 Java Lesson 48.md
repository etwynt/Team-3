## TEAMWORK: 

1. Write a MOCK test
   
   1.1. When there is only one city with 100 citizens, then choose this city
   
   1.2. When there is no seed provided, then choose whatever city
   
2. Look how to check for exceptions
   
     2.1. When there is negative citizen count in a city, then show exception
  
     2.2. When there is no cities provided, then show exception

**MOCK test for 1.1**

```java
@Test
    void WHEN_OneCity100_THEN_ChooseThisCity() throws Exception {
        ICityRepository cityRepository = Mockito.mock(ICityRepository.class);
        CityService cityService = new CityService(cityRepository);

        ArrayList<City> cities = new ArrayList<>();
        cities.add(new City("Onecity", 100));

        when(cityRepository.getCities()).thenReturn(cities);

        var randomCity = cityService.getRandomCity();

        Assert.isTrue(randomCity.getName().equals("Onecity"), "Onecity is supposed to be chosen.");
    }
```

**MOCK test for 1.2**

```java
@Test
    void WHEN_NoSeedProvided_THEN_ChooseWhateverCity() throws Exception {
        ICityRepository cityRepository = Mockito.mock(ICityRepository.class);
        CityService cityService = new CityService(cityRepository);

        ArrayList<City> cities = new ArrayList<>();
        cities.add(new City("Tallinn", 150));
        cities.add(new City("Viljandi", 50));

        when(cityRepository.getCities()).thenReturn(cities);

        var randomCity = cityService.getRandomCity();

        boolean isValidCity = randomCity.getName().equals("Tallinn") || randomCity.getName().equals("Viljandi");
        Assert.isTrue(isValidCity, "A valid city should be chosen.");
    }
```

**2.1 Handling negative citizen count**

We created a new method in CityService.java
```java
public void validCities(ArrayList<City> cities) {
        for (City city : cities) {
            if (city.getPopulation() < 0) {
                throw new IllegalArgumentException("City cannot have a negative population: " + city.getName());
            }
        }
    }
```
This method is used in getRandomCity() method
```java
public City getRandomCity() throws Exception {
        ArrayList<City> cities = cityRepository.getCities();

        validCities(cities);
        int totalCitizenCount = getCitizenCount(cities);
        int randomValue = getRandomNumber(totalCitizenCount);
        City city = getCity(cities, randomValue);

        return city;
    }
```

**2.2 Handling no cities provided**

We updated the validCities() method to also check if the provided cities list is empty or the cities list is not provided at all (cities == null). The check for an empty or missing list of cities comes before validating negative populations because we can only assess populations if there are cities to check. If the list is either empty or not provided, the first IllegalArgumentException is triggered, preventing further population validation.

```java
public void validCities(ArrayList<City> cities) {
        if (cities.isEmpty() || cities == null) {
            throw new IllegalArgumentException("No cities available.");
        }

        for (City city : cities) {
            if (city.getPopulation() < 0) {
                throw new IllegalArgumentException("City cannot have a negative population: " + city.getName());
            }
        }
    }
```
