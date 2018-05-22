### Groovy Script Cheat Sheet

## Compare scripts using response
```
import net.sf.json.groovy.JsonSlurper

def jsonSlurper = new JsonSlurper()
def result = jsonSlurper.parseText(context.response)
def pin = result.debtorPin
assert pin==null;
```



Iterate through an array
```
import net.sf.json.groovy.JsonSlurper

def jsonSlurper = new JsonSlurper()
def result = jsonSlurper.parseText(context.response)
def itemsList = result.items;

itemsList.each{
    assert it.status == "registered"
}
```

Date Conversion Script
```
import net.sf.json.groovy.JsonSlurper

def stringFormat = "yyyy-MM-dd'T'hh:mm:ss.SSSXXX"
def jsonSlurper = new JsonSlurper()
def result = jsonSlurper.parseText(context.response)
def createdDate = context.expand( result.verificationStatement.loadedDate )
def udpatedDate = context.expand( result.verificationStatement.lastUpdatedDate )
//log.info loadedDate
assert createdDate != null
assert udpatedDate != null

//change to yyyy-MM-dd'T'hh:mm:ss.SSS'Z'Z if using JAVA8
try {
    date = new Date().parse(stringFormat, createdDate)
    date = new Date().parse(stringFormat, udpatedDate)
} catch (Exception e) {
     assert false
}
```

Date is greater than 5 years
```
Date d = new Date() + 1825;
def stringFormat = "yyyy-MM-dd'T'hh:mm:ss.SSSXXX"
def expiryDate = context.expand( '${RenewFS#Response#$[\'verificationStatement\'][\'expiryDate\']}' )

try {
    Date date = new Date().parse(stringFormat, expiryDate)
    assert d<date
} catch (Exception e) {
     assert false
}
```

Set Header to a Test case property from script assertion
```
assert messageExchange.responseHeaders["ETag"] != null
if(messageExchange.responseHeaders["ETag"] != null){
    messageExchange.modelItem.testStep.testCase.setPropertyValue( "etag", messageExchange.responseHeaders["ETag"][0].toString() )
}
```

Retrieve http status code from response header from groovy script
```
def httpResponseHeaders = context.testCase.testSteps["TestCaseName"].testRequest.response.responseHeaders
def httpStatus = httpResponseHeaders["#status#"]
def httpStatusCode = (httpStatus =~ "[1-5]\\d\\d")[0]

log.info httpStatusCode
```


Setting up Data
```
Random random = new Random();
char[] alphaNumbericChars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789".toCharArray();
char[] vinChars = "ABCDEFGHJKLMNPRSTUVWXYZ0123456789".toCharArray();
char[] numericChars = "0123456789".toCharArray();
char[] alphaChars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".toCharArray();
char[] serialChars = "AZWF0123456789".toCharArray();

//Start Generated VIN
StringBuilder vinSb = new StringBuilder();
for (int i = 0; i < 17; i++) {
    char c = vinChars[random.nextInt(vinChars.length)];
    vinSb.append(c);
}
testRunner.testCase.setPropertyValue( "generatedVIN", vinSb.toString() )
//End Generated VIN


//Start Generated CHASIS
StringBuilder chassisSb = new StringBuilder();
for (int i = 0; i < 5; i++) {
    char c = alphaNumbericChars[random.nextInt(alphaNumbericChars.length)];
    chassisSb.append(c);
}
String chassisPart1 = chassisSb.toString();
chassisSb = new StringBuilder();
for (int i = 0; i < 6; i++) {
    char c = numericChars[random.nextInt(numericChars.length)];
    chassisSb.append(c);
}
String chassisPart2 = chassisSb.toString();
testRunner.testCase.setPropertyValue( "generatedChassis", chassisPart1+"-"+chassisPart2 )
//End Generated CHASIS


//Start Generated MARK
StringBuilder markSb = new StringBuilder();
for (int i = 0; i < 3; i++) {
    char c = alphaChars[random.nextInt(alphaChars.length)];
    markSb.append(c);
}
testRunner.testCase.setPropertyValue( "generatedMark", "ZK-"+markSb.toString() )
//End Generated MARK

//Start Generated SERIAL
StringBuilder serialSb = new StringBuilder();
for (int i = 0; i < 8; i++) {
    char c = serialChars[random.nextInt(serialChars.length)];
    serialSb.append(c);
}
testRunner.testCase.setPropertyValue( "generatedSerial", serialSb.toString() )
//End Generated SERIAL

//Start Generated Random CITY
List<String> list = new ArrayList<String>();
list.add("Auckland");
list.add("Wellington");
list.add("Christchurch");
list.add("Hamilton");
list.add("Tauranga");
list.add("Napier-Hastings");
list.add("Dunedin");
list.add("Palmerston North");
list.add("Nelson");
list.add("Rotorua");
list.add("New Plymouth");
list.add("Invercargill");
list.add("Whangarei");
list.add("Invercargill");
list.add("Whanganui");
list.add("Gisborne");
String city = list.get(new Random().nextInt(list.size()));
testRunner.testCase.setPropertyValue( "city", city )
//End Generated Random CITY

//START Random NAME Generator
String[] nameArray = ["Aaron","Adam","Adrian","Aiden","Alex","Alexander","Andrew","Angel","Anthony","Austin","Ayden","Benjamin","Bentley","Blake","Brandon","Brayden","Brody","Bryson","Caleb","Cameron","Carlos","Carson","Carter","Charles","Chase","Christian","Christopher","Colton","Connor","Cooper","Damian","Daniel","David","Dominic","Dylan","Easton","Eli","Elijah","Ethan","Evan","Gabriel","Gavin","Grayson","Henry","Hudson","Hunter","Ian","Isaac","Isaiah","Jace","Jack","Jackson","Jacob","James","Jason","Jaxon","Jayden","Jeremiah","John","Jonathan","Jordan","Jose","Joseph","Joshua","Josiah","Juan","Julian","Justin","Kayden","Kevin","Landon","Levi","Liam","Logan","Lucas","Luis","Luke","Mason","Matthew","Michael","Nathan","Nathaniel","Nicholas","Noah","Nolan","Oliver","Owen","Parker","Robert","Ryan","Ryder","Samuel","Sebastian","Thomas","Tristan","Tyler","William","Wyatt","Xavier","Zachary","Aaliyah","Abigail","Addison","Alexa","Alexandra","Alexis","Allison","Alyssa","Amelia","Andrea","Anna","Annabelle","Aria","Ariana","Arianna","Ashley","Aubree","Aubrey","Audrey","Autumn","Ava","Avery","Bailey","Bella","Brianna","Brooklyn","Camila","Caroline","Charlotte","Chloe","Claire","Elizabeth","Ella","Ellie","Emily","Emma","Eva","Evelyn","Faith","Gabriella","Genesis","Gianna","Grace","Hailey","Hannah","Harper","Isabella","Jasmine","Jocelyn","Julia","Katherine","Kayla","Kaylee","Kennedy","Khloe","Kimberly","Kylie","Lauren","Layla","Leah","Lillian","Lily","London","Lucy","Lydia","Mackenzie","Madeline","Madelyn","Madison","Makayla","Maya","Melanie","Mia","Molly","Morgan","Naomi","Natalie","Nevaeh","Olivia","Peyton","Piper","Reagan","Riley","Samantha","Sarah","Savannah","Scarlett","Serenity","Skylar","Sofia","Sophia","Sophie","Stella","Sydney","Taylor","Trinity","Victoria","Violet","Zoe","Zoey"]
String[] lastNameArray = ["Abbott","Acevedo","Acosta","Adams","Adkins","Aguilar","Aguirre","Albert","Alexander","Alford","Allen","Allison","Alston","Alvarado","Alvarez","Anderson","Andrews","Anthony","Armstrong","Arnold","Ashley","Atkins","Atkinson","Austin","Avery","Avila","Ayala","Ayers","Bailey","Baird","Baker","Baldwin","Ball","Ballard","Banks","Barber","Barker","Barlow","Barnes","Barnett","Barr","Barrera","Barrett","Barron","Barry","Bartlett","Barton","Bass","Bates","Battle","Bauer","Baxter","Beach","Bean","Beard","Beasley","Beck","Becker","Bell","Bender","Benjamin","Bowman","Boyd","Boyer","Boyle","Bradford","Bradley","Bradshaw","Brady","Branch","Bray","Brennan","Brewer","Bridges","Briggs","Bright","Britt","Brock","Brooks","Brown","Browning","Bruce","Bryan","Bryant","Buchanan","Buck","Buckley","Buckner","Bullock","Burch","Burgess","Burke","Burks","Burnett","Burns","Burris","Burt","Burton","Bush","Butler","Byers","Byrd","Cabrera","Cain","Calderon","Caldwell","Calhoun","Callahan","Camacho","Cameron","Campbell","Campos","Cannon","Cantrell","Cantu","Cardenas","Carey","Carlson","Carney","Carpenter","Carr","Carrillo","Carroll","Carson","Carter","Carver","Case","Casey","Cash","Castaneda","Castillo","Castro","Cervantes","Chambers","Chan","Chandler","Chaney","Chang","Chapman","Charles","Chase","Chavez","Chen","Cherry","Christensen","Christian","Church","Clark","Cortez","Cote","Cotton","Cox","Craft","Craig","Crane"," Dennis","Diaz","Dickerson","Dickson","Dillard","Dillon","Dixon","Dodson","Dominguez","Donaldson","Donovan","Dorsey","Dotson","Douglas","Downs","Doyle","Drake","Dudley","Duffy","Duke","Duncan","Dunlap","Dunn","Duran","Durham","Dyer","Eaton","Edwards","Elliott","Ellis","Ellison","Emerson","England","English","Erickson","Espinoza","Estes","Estrada","Evans","Everett","Ewing","Farley","Farmer","Farrell","Faulkner","Ferguson","Fernandez","Ferrell","Fields","Figueroa","Finch","Finley","Fischer","Fisher","Fitzgerald","Fitzpatrick","Fleming","Fletcher","Flores","Flowers","Floyd","Flynn","Foley","Forbes","Ford","Foreman","Foster","Fowler","Fox","Francis","Franco","Frank","Franklin","Franks","Frazier","Frederick","Freeman","French","Frost","Fry","Frye","Fuentes","Fuller","Fulton","Gaines","Glass","Glenn","Glover","Goff","Golden","Gomez","Gonzales","Gonzalez","Good","Hammond","Hampton","Hancock","Haney","Hansen","Hanson","Hardin","Harding","Hardy","Harmon","Harper","Harrell","Harrington","Harris","Harrison","Hart","Hartman","Harvey","Hatfield","Hawkins","Hayden","Hayes","Haynes","Hays","Head","Heath","Hebert","Henderson","Hendricks","Hendrix","Henry","Hensley","Henson","Herman","Hernandez","Herrera","Herring","Hess","Hester","Hewitt","Hickman","Hicks","Higgins","Hill","Hines","Hinton","Hobbs","Hodge","Hodges","Hoffman","Hogan","Holcomb","Holden","Holder","Holland","Holloway","Holman","Holmes","Holt","Hood","Hooper","Hoover","Hopkins","Hopper","Horn","Horne","Horton","House","Houston","Howard","Howe","Howell","Hubbard","Huber","Hudson","Huff","Huffman","Hughes","Hull","Humphrey","Hunt","Hunter","Hurley","Hurst","Hutchinson","Hyde","Ingram","Irwin","Jackson","Jacobs","Jacobson","James","Jarvis","Jefferson","Jenkins","Jennings","Jensen","Jimenez","Johns","Johnson","Johnston","Jones","Jordan","Joseph","Joyce","Joyner","Juarez","Justice","Kane","Kaufman","Keith","Keller","Kelley","Kelly","Kemp","Kennedy","Kent","Kerr","Key","Kidd","Kim","King","Kinney","Kirby","Kirk","Kirkland","Klein","Kline","Knapp","Knight","Knowles","Knox","Koch","Kramer","Lamb","Lambert","Lancaster","Landry","Lane","Lang","Langley","Lara","Larsen","Larson","Lawrence","Lawson","Le","Leach","Leblanc","Lee","Leon","Leonard,Mann","Manning","Marks","Marquez","Marsh","Marshall","Martin","Martinez","Mason","Massey","Mathews","Mathis","Matthews","Maxwell","May","Mayer","Maynard","Mayo","Mays","Mcbride","Mccall","Mccarthy","Mccarty","Mcclain","Mcclure","Mcconnell","Mccormick","Mccoy","Mccray","Mcpherson","Meadows","Medina","Mejia","Melendez","Melton","Mendez","Mendoza","Mercado","Mercer","Merrill","Merritt","Meyer","Meyers","Michael","Middleton","Miles","Miller","Mills","Miranda","Mitchell","Molina","Monroe","Montgomery","Montoya","Moody","Moon","Mooney","Moore","Morales","Moran","Moreno","Morgan","Morin","Morris","Morrison","Morrow","Morse","Morton","Moses","Mosley","Moss","Mueller","Mullen","Mullins","Munoz","Murphy","Murray","Myers","Nash","Navarro","Neal","Nelson","Newman","Newton","Nguyen","Nichols","Nicholson","Nielsen","Nieves","Nixon","Noble","Noel","Nolan","Norman","Norris","Norton","Nunez","Obrien","Ochoa","Oconnor","Odom","Odonnell","Oliver","Olsen","Olson","Oneal","Oneil","Oneill","Orr","Ortega","Ortiz","Osborn","Osborne","Owen","Owens","Pace","Pacheco","Padilla","Page","Palmer","Park","Parker","Parks","Parrish","Parsons","Pate","Patel","Patrick","Patterson","Patton","Paul","Payne","Pearson","Peck","Pena","Pennington","Perez","Perkins","Perry","Peters","Petersen","Peterson","Petty","Phelps","Phillips","Pickett","Pierce","Pittman","Pitts","Reeves","Reid","Reilly","Reyes","Reynolds","Rhodes","Rice","Rich","Richard","Richards","Richardson","Richmond","Riddle","Riggs","Riley","Rios","Rivas","Rivera","Rivers","Roach","Robbins","Roberson","Roberts","Robertson","Robinson","Robles","Rocha","Rodgers","Rodriguez","Rodriquez","Rogers","Rojas","Rollins","Roman","Romero","Rosa","Rosales","Rosario","Rose","Ross","Roth","Rowe","Rowland","Roy","Ruiz","Rush","Russell","Russo","Rutledge","Ryan","Salas","Salazar","Salinas","Sampson","Sanchez","Sanders","Sandoval","Sanford","Santana","Santiago","Santos","Sargent","Saunders","Savage","Sawyer","Schmidt","Schneider","Schroeder","Schultz","Schwartz","Scott","Sears","Sellers","Serrano","Sexton","Shaffer","Shannon","Sharp","Sharpe","Shaw","Shelton","Shepard","Shepherd","Sheppard","Sherman","Shields,Steele","Stein","Stephens","Stephenson","Stevens","Stevenson","Stewart","Stokes","Stone","Stout","Strickland","Strong","Stuart","Suarez","Sullivan","Summers","Sutton","Swanson","Sweeney","Sweet","Sykes","Talley","Tanner","Tate","Taylor","Terrell","Terry","Thomas","Vazquez","Vega","Velasquez","Velazquez","Velez","Villarreal","Vincent","Vinson","Wade","Wagner","Walker","Wall","Wallace","Waller","Walls","Walsh","Walter","Walters","Walton","Ward","Ware","Warner","Warren","Washington","Waters","Watkins","Watson","Watts","Weaver","Webb","Weber","Webster","Weeks","Weiss","Welch","Wells","West","Wheeler","Whitaker","White","Whitehead","Whitfield","Whitley","Whitney","Wiggins","Wilcox","Wilder","Wiley","Wilkerson","Wilkins","Wilkinson","William","Williams","Williamson","Willis","Wilson","Winters","Wise","Witt","Wolf","Wolfe","Wong","Wood","Woodard","Woods","Woodward","Wooten","Workman","Wright","Wyatt","Wynn","Yang","Yates","York","Young","Zamora","Zimmerman"]
def nameList = nameArray.toList()
def lastNameList = lastNameArray.toList()
testRunner.testCase.setPropertyValue( "firstName", nameList.get(new Random().nextInt(nameList.size())))
testRunner.testCase.setPropertyValue( "middleNames", nameList.get(new Random().nextInt(nameList.size())))
testRunner.testCase.setPropertyValue( "lastName", lastNameList.get(new Random().nextInt(lastNameList.size())))
//END Random NAME Generator
```
