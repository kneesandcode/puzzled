<html>
    <!-- 2025 by Thomas Colthurst -->
    <!-- Original at https://thomaswc.com/2025.html -->
    <!-- Released under a Creative Commons Share Alike with Attribution License -->
    <!-- See https://creativecommons.org/licenses/by-sa/4.0/ -->
    <title>2025</title>
    <style>
        body {
  background-color: white; /* Or #fff or #ffffff */
}

.bigbut {
width:100%; height:100%; display:block;
}

.shake {
  animation: shake 0.5s;
  animation-iteration-count: 1;
}

.numCirc {
    border-radius: 70%;
    background: white; /* #fff; */
    border: 2px solid red; /* #666; */
    color: red; /* #666; */
    text-align: center;
}

.red {
  color: red;
}

.floating {
    position: fixed;
    z-index: 999;
    background-color: lightblue;
}

#fireworks {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 10000;
        }

@keyframes shake {
  0% { transform: translateX(1px) rotate(0deg); }
  10% { transform: translateX(-1px) rotate(-1deg); }
  20% { transform: translateX(-3px) rotate(1deg); }
  30% { transform: translateX(3px) rotate(0deg); }
  40% { transform: translateX(1px) rotate(1deg); }
  50% { transform: translateX(-1px) rotate(-1deg); }
  60% { transform: translateX(-3px) rotate(0deg); }
  70% { transform: translateX(3px) rotate(-1deg); }
  80% { transform: translateX(-1px) rotate(1deg); }
  90% { transform: translateX(1px) rotate(0deg); }
  100% { transform: translateX(1px) rotate(-1deg); }
}
    </style>
    <body>
        <canvas id="fireworks"></canvas>
<div class="floating">
   Make 45 groups of 45!  (By combining two at a time.) &nbsp;
   Score: <span id="score">0</span> &nbsp; Mistakes: <span id="mistakes">0</span> &nbsp;
    <button type="button" id="deselect" style="" disabled onclick="deselect()">Deselect</button>
</div>
<div style="padding-top: 16px; position: absolute" id="board"></div>

    </body>
        <script>
        // Bugs: 
        // 1) Unicode not rendering properly inside buttons on Chrome.
        // Feature requests:
        // 1) Don't penalize already guessed.
        
        // Globals
        const M = 45;  // Board size
            
        var score = 0;
        var mistakes = 0;
        
        var selected = 0;  // The currently selected button, if any.
        
        var wordlist = [];  // Actually, a list of (word, category) pairs
    

        // The categories.
        
        var cats = {};
            
            // #1.  Censored:  
            cats["Gilbert & Sullivan Characters"] = ["The Rt. Hon. Sir Joseph Porter", "Captain Corcoran", "Ralph Rackstraw", "Dick Deadeye", "Bill Bobstay", "Mrs Cripps", "Major Murgatroyd", "Reginald Bunthorne", "The Pirate King", "Sir Marmaduke Pointdextre", "Lady Sangazure", "Iolanthe", "Strephon", "George, Earl of Mountararat", "King Hildebrand", "Hilarion", "King Gama", "Princess Ida", "Lady Psyche", "Sacharissa", "Nanki-Poo", "Yum-Yum", "Ko-Ko", "Pooh-Bah", "Pish-Tush", "Peep-Bo", "Pitti-Sing", "Richard Dauntless", "Sir Despard Murgatroyd", "Old Adam Goodheart", "Rose Maybud", "Mad Margaret", "Sergeant Meryll", "Wilfred Shadbolt", "Elsie Maynard", "Jack Point", "Colonel Fairfax", "The Duke of Plaza-Toro", "Don Alhambra del Bolero", "Marco Palmieri", "Katisha", "Major-General Stanley", "Dame Carruthers", "Go-To", "The Mikado of Japan"];
            
            // #2
            cats["Cat Breeds"] = ["Abyssinian", "American bobtail", "American wirehair", "Aphrodite Giant", "Arabian Mau", "Asian semi-longhair", "Australian Mist", "Balinese", "Bambino", "Bengal", "Birman", "Burmese", "Burmilla", "California Spangled", "Chantilly-Tiffany", "Chausie", "Colorpoint Shorthair", "Cornish Rex", "Cymric", "Devon Rex", "Donskoy", "York Chocolate", "Foldex", "Havana Brown", "Highlander", "Javanese", "Kanaani", "Khao Manee", "Kinkalow", "Korat", "Lambkin", "Turkish Angora", "Lykol", "Maine Coon", "Thai Lilac", "Neva Masquerade", "Oriental Bicolor", "Ragdoll", "Russian Blue", "Savannah", "Scottish Fold", "Serengeti", "Siamese", "Snowshoe", "Sokoke"];
            
            // #3.  Censored: 
            cats["German Loanwords"] = ["Bratwurst", "Delicatessen", "Frankfurter", "Kohlrabi", "Lager", "Muesli", "Noodle", "Pumpernickel", "Quark", "Strudel", "Abseil", "Blitz", "Karabiner", "Rucksack", "Dachshund", "Haster", "Poddle", "Rottweiler", "Neanderthal", "Schadenfreude", "Wanderlust", "Zeitgeist", "Doppelgänger", "Kindergarten", "Kitsch", "Poltergeist", "Wunderkind", "Ersatz", "Flak", "Zeppelin", "Kaput", "Nix", "Verboten", "Gestalt", "Glockenspiel", "Leitmotif", "Sitzprobe", "Verfremsdungseffekt", "Zugzwang", "Hinterland", "Karst", "Bildungsroman", "Putsch", "Weltanschauung", "Realpolitik"];

            // #4.  Censored: 
            cats["National Trust Properties Seen on Screen"] = ["Botallack", "Holywell", "St Michael's Mount", "Antony", "Greenway", "Saltram", "Montacute House", "Barrington Court", "Tyntesfield", "Brean Down", "Great Chalfield Manor", "Lacock Abbey", "Stourhead", "Dyrham Park", "Woodchester Park", "Chastleton House", "Basildon Park", "Cliveden", "Stowe", "Claydon", "Ashridge Estate", "Frensham Little Pond", "Petwork", "Lamb House", "Scotney Castle", "Chartwell", "Knole", "Fenton House", "Osterley Park", "Ham House", "Wimpole Estate", "Lavenham Guildhall", "Felbrigg Hall", "Packwood House", "Benthall Hall", "Belton House", "Hardwick", "Freshwater West and Gupton Farm", "Dyffryn Gardens", "Tredegar House", "Henrhyd Falls", "Bodanant Garden", "Penrhyn Castle", "Castle Ward", "Fountains Abbey and Studley Water Garden"];
            
            // #5
            cats["JMW Turner Paintings"] = ["The Fighting Temeraire", "Rain, Steam and Speed", "The Slave Ship", "Snow Storm: Steam-Boat off a Harbour's Mouth", "The Burning of the Houses of Lords and Commons", "Fishermen at Sea", "Dido Building Carthage", "Norham Castle, Sunrise", "The Shipwreck", "Calais Pier", "Light and Colour (Goethe's Theory)", "Frosty Morning", "Venice from the Porch of Madonna della Salute", "Sun Rising through Vapour", "Ulysses Deriding Polyphemus", "London from Greenwich Park", "The Battle of Trafalgar, as seen from the Mizen Starboard Shrouds of the Victory", "Rome, from Mount Aventine", "Chichester Canal", "The Fifth Plague of Egypt", "Modern Rome - Campo Vaccino", "Regulus", "Rome, from the Vatican", "Snow Storm: Hannibal and his Army Crossing the Alps", "The Rigi", "The Scarlet Sunset", "Venice from the Giudecca", "Helvoetsluys", "The Rising Squall, Hot Wells", "The Harbour of Dieppe", "Keelmen Heaving in Coals by Moonlight", "Yacht Approaching the Coast", "Three Seascapes", "Margate", "The Angel Standing in the Sun", "Crossing the Brook", "Peace - Burial at Sea", "Sunrise with Sea Monsters", "Sun Setting over a Lake", "Mortlake Terrace: Early Summer Morning", "Fort Vimieux", "Dutch Boats in a Gale", "The Field of Waterloo", "Dort or Dordrecht: The Dort Packet from Rotterdam Becalmed", "Sheerness as Seen from the Nore"];

            // #6
            cats["Oxford Colleges"] = ["Balliol", "Blackfriars", "Brasenose", "Campion", "Christ Church", "Corpus Christi", "Exeter", "Green Templeton", "Harris Manchester", "Hertford", "Jesus", "Keble", "Kellogg", "Lady Margaret Hall", "Linacre", "Lincoln", "Magdalen", "Mansfield", "Merton", "New", "Nuffield", "Oriel", "Pembroke", "Regent's Park", "Reuben", "Somerville", "St Anne's", "St Antony's", "St Catherine's", "St Cross", "St Edmund", "St Hilda's", "St Hugh's", "St John's", "St Peter's", "The Queen's", "Trinity", "University", "Wadham", "Wolfson", "Worcester", "Wycliffe", "Ripon College Cuddesdon", "St Stephen's House", "St Hilda's (again)"];

             // #7.  Censored: 
             cats["Ralph Fiennes Films"] = ["28 Years Later", "The Choral", "The Return", "Conclave", "The Wonderful Story of Henry Sugar", "The Menu", "The King's Man", "No Time to Die", "The Dig", "Official Secrets", "Holmes & Watson", "The White Crow", "Sea Sorrow", "The Lego Batman Movie", "Hail, Caesar!", "Spectre", "A Bigger Splaah", "Two Women", "The Grand Budapest Hotel", "The Invisible Woman", "Great Expectations", "Wrath of the Titans", "Coriolanus", "Harry Potter and the Order of the Phoenix", "Cemetery Junction", "Nanny McPhee Returns", "The Reader", "The Duchess", "In Bruges", "The Constant Gardener", "Maid in Manhattan", "Red Dragon", "Spider", "The End of the Affair", "Onegin", "The Prince of Egypt", "The Avengers", "Oscar and Lucinda", "The English Patient", "Strange Days", "Quiz Show", "Schindler's List", "Wuthering Heights", "The Baby of Mâcon", "Sunshine"];
             
             // #8.  
             cats["Famous Cats (real and fictional)"] = ["Félicette", "Tuxedo Stan", "Bobby Kertanegara", "Paddles", "Luffar-Lasse", "Beerbohm", "Catmando", "Crimean Tom", "Hamish McHamish", "Tibs the Great", "Unsinkable Sam", "Socks Clinton", "Jorts and Jean", "Grumpy Cat", "Lil Bub", "Maru", "Longcat", "Sockington", "Tsim Tung Brother Cream", "Colonel Meow", "Prince Chunk", "Mrs Chippy", "Choupette", "Nedjem", "Muezza", "Cheshire Cat", "Nelson", "Simpkin", "Tiddles", "Misty Malarky Ying Yang", "Dusty the Klepto Kitty", "Little Nicky", "Smudge", "Palmerston", "Garfield", "Mademoiselle Fifi", "Salem", "Wilberforce", "Jonesy", "Mr Bigglesworth", "Crookshanks", "Mrs Norris", "Pyewacket", "Zunar-J5", "Thackery Binx"];


              // #9.  Censored:  
              cats["Crime Fiction Writers"] = ["Boris Akunin", "Kate Atkinson", "Mark Billingham", "John Dickson Carr", "Raymond Chandler", "GK Chesterton", "Lee Child", "Agatha Christie", "Mary Higgins Clark", "Harlan Coben", "Patricia Cornwell", "Colin Dexter", "Arthur Conan Doyle", "Umberto Eco", "Antonia Fraser", "Tana French", "Dashiell Hammett", "Patricia Highsmith", "Reginald Hill", "Arnaldur Indriðason", "PD James", "Stieg Larsson", "Mick Herron", "John le Carré", "Donna Leon", "Gaston Leroux", "Henning Mankell", "Alexander McCall Smith", "Val McDermid", "Denise Mina", "Jo Nesbø ", "Sara Paretsky", "Ellis Peters", "Edgar Allan Poe", "Ellery Queen", "Ian Rankin", "Kathy Reichs", "Ruth Rendell", "Peter Robinson", "CJ Sansom", "Karin Slaughter", "Martin Cruz Smith", "Andrew Taylor", "Dorothy L Sayers", "Josephine Tey"];
              
              // #10
              cats["Latin Names of Plants and Flowers"] = ["Agapanthus", "Laurus nobilis", "Rudbeckia", "Rubus", "Buddleia", "Helleborus niger", "Echinacea", "Geranium", "Helichrysum", "Anthriscus", "Hemerocallis", "Bergenia", "Ribes", "Digitalis", "Forsythia", "Ulex", "Aquilegia", "Eucalyptus", "Calluna", "Ilex", "Lonicera", "Jasminum", "Lavandula", "Syringa", "Lavatera", "Vinca", "Hosta", "Papaver", "Kniphofia", "Cistus", "Perovskia", "Salvia", "Hebe", "Astelia", "Galanthus", "Gladiolus", "Trachelospermum", "Euphorbia", "Fiscus", "Hamamelis", "Ranunculus acris", "Bellis perennis", "Taraxacum originale", "Viola arvensis", "Primula"];
              
              // #11 Censored: 
              cats["Sewing and Knitting Terms"] = ["Blocking", "Cast off", "Cable", "Entrelac", "Fair Isle", "Intarsia", "Jog", "Mosaic", "Leading leg", "Fisherman's rib", "Steeking", "Frogging", "Brioche", "Purl", "Slip", "Garter", "Selvedge", "Stockinette", "Skein", "Binding", "Boning", "Bumblebunching", "Charm square", "Grain", "Interlocking", "Ladder", "Nap", "Notches", "Darts", "Notions", "Pinking", "Ripping", "Ruffler", "Shirring", "Smocking", "Tacking", "Bobbin", "Warp", "Weft", "Zip", "Hem", "Seam", "Felting", "Feed-dogs", "Appliqué"];
              
              // #12.  Censored:  
              cats["The Archers Characters"] = ["Jill Archer", "Christine Barford", "Leonard Berry", "Clive Horrobin", "Matt Crawford", "Lilian Bellamy", "Shula Hebden Lloyd", "Caroline Bone", "Kenton Archer", "Elizabeth Pargetter", "Helen Titchener", "Brian Aldridge", "Brenda Tucker", "Robin Fairbrother", "Rúairi Donovan", "Clarrie Grundy", "Barbarella", "Emma Carter", "Oliver Sterling", "Siobhán Hathaway", "Adam Macy", "Paddy Redmond", "Simon Gerrard", "Rob Titchener", "Lucas Madikane", "Tracey Horrobin", "Roy Tucker", "Nigel Pargetter", "Sergeant Harrison Burns", "Ian Craig", "Alan Franks", "Usha Gupta", "Richard Locke", "Jack "Jazzer" McCreary", "Kirsty Miller", "Stella Pryor", "Fallon Rogers", "Lynda Snell", "Lilian Bellamy", "Carol Tregorran", "Marjorie Antrobus", "Bert Fry", "Nelson Gabriel", "Sid Perks", "Polly Perkins"];
              
              // #13.  Censored: 
              cats["Buckinghamshire Villages"] = ["Barton Hartshorn", "Bellingdon", "Boarstall", "Bow Brickhill", "Brill", "Cadmore", "Castlethorpe", "Chartridge", "Cholesbury", "Clifton Reynes", "Cublington", "Dagnall", "Drayton Beauchamp", "Edgcott", "Emberton", "Fawley Bottom", "Fingest", "Fulmer", "Gawcott", "Great Horwood", "Grendon Underwood", "Haddenham", "Hambleden", "Hazlemere", "Hoggeston", "Hyde Heath", "Iver", "Jordans", "Kingsey", "Leckhampstead", "Ley Hill", "Marsh Gibbon", "Nether Winchendon", "Newton Blossombille", "Oving", "Padbury", "Quainton", "Ravrnstone", "Shabbington", "Speen", "Tylers Green", "Stoke Poges", "Taplow", "Pitchcott", "Mentmore"];
              
              // #14.  Censored:  
              cats["Commonly Mispelled Words, Misspelled"] = ["accomodate", "adress", "adviseable", "agressive", "anually", "becuase", "bouy", "cemetary", "commited", "conceed", "definately", "diffrence", "embarass", "fourty", "independant", "jewelery", "milennium", "miniscule", "neccessary", "occassion", "occured", "possesion", "pronounciation", "questionaire", "recieve", "restaraunt", "rythm", "seperate", "succesful", "suprise", "tommorrow", "untill", "wierd", "withold", "dosen't", "desparate", "dissapoint", "gratefull", "humerous", "inteligence", "momento", "mischevious", "ommision", "privelege", "tomatos"];
              
              // #15
              cats["US VPs"] = ["John Adams", "Thomas Jefferson", "Aaron Burr", "George Clinton", "Elbridge Gerry", "Daniel Tompkins", "John Calhoun", "Martin Van Buren", "Richard Mentor Johnson", "John Tyler", "George Dallas", "Millard Fillmore", "William King", "John Breckinridge", "Hannibal Hamlin", "Andrew Johnson", "Schuyler Colfax", "Henry Wilson", "William Wheeler", "Chester Arthur", "Thomas Hendricks", "Levi Morton", "Adlai Stevenson", "Garret Hobart", "Theodore Roosevelt", "Charles Fairbanks", "James Sherman", "Thomas Marshall", "Calvin Coolidge", "Charles Dawes", "Charles Curtis", "John Nance Garner", "Henry Wallace", "Harry Truman", "Alben Barkley", "Richard Nixon", "Lyndon Johnson", "Hubert Humphrey", "Spiro Agnew", "Gerald Ford", "Nelson Rockefeller", "Walter Mondale", "George H. W. Bush", "Dan Quayle", "Al Gore", "Dick Cheney", "Joe Biden", "Mike Pence", "Kamala Harris", "J. D. Vance"];
              
              // #16
              cats["SNL Cast Members"] = ["Dana Carvey", "Bill Hader", "Eddie Murphy", "Chevy Chase", "Kevin Nealon", "Norm MacDonald", "Tina Fey", "Amy Poehler", "Mikey Day", "Kenan Thompson", "John Belushi", "Mike Myers", "Michael Che", "Kate McKinnon", "Pete Davidson", "Aidy Bryant", "Colin Jost", "Cecily Strong", "Heidi Gardner", "Dan Aykroyd", "Kyle Mooney", "Will Ferrell", "Gilda Radner", "Sarah Sherman", "Beck Bennett", "Bowen Yang", "Chris Farley", "Kristen Wiig", "Bill Murray", "Jane Curtin", "Chloe Fineman", "Maya Rudolph", "Phil Hartman", "Jimmy Fallon", "Seth Meyers", "Vanessa Bayer", "Andy Samberg", "Fred Armisen", "Adam Sandler", "Rachel Dratch", "David Spade", "James Austin Johnson", "Alex Moffat", "Jason Sudeikis", "Jon Lovitz", "Jan Hooks", "Dennis Miller", "Chris Parnell", "Molly Shannon", "Taran Killam", "Jane Curtin", "Will Forte", "Tracy Morgan"];
              
              // #17
              cats["World Wonders"] = ["Statue of Liberty", "Taj Mahal", "Eiffel Tower", "Golden Gate Bridge", "Colosseum", "Great Wall of China", "Great Pyramid of Giza", "Angkor Wat", "Machu Picchu", "Leaning Tower of Pisa", "Mount Rushmore", "Empire State Building", "Grand Canyon", "Sydney Opera House", "The Acropolis", "Christ the Redeemer", "Hollywood Sign", "Niagara Falls", "La Sagrada Familia", "Stonehenge", "Big Ben", "Lincoln Memorial", "Mount Fuji", "London Eye", "St. Peter's Basilica", "Notre Dame", "Tokyo Tower", "St. Basil's Cathedral", "Arc de Triomphe", "The Great Sphinx", "Mount Kilimanjaro", "The Forbidden City", "Mount Everest", "Sears Tower", "Brooklyn Bridge", "Trevi Fountain", "Burj Al Arab Hotel", "Louvre Museum", "Times Square", "The White House", "Buckingham Palace", "Versailles", "Neuschwanstein Castle", "Pompeii", "Edinburgh Castle", "Matterhorn", "CN Tower"];
              
              // #18.  Censored:  Agriculture (Invention), Public Health (Medical Specialty), Finance (Google Product)
              cats["College Majors"] = ["Business", "Nursing", "Psychology", "Communications", "Biology", "Marketing", "Accounting", "Criminal Justice", "English", "Computer Science", "Political Science", "Education", "Economics", "Physical Fitness", "History", "Graphic Design", "Sociology", "Mechanical Engineering", "Electrical Engineering", "Fine Arts", "Liberal Arts", "Journalism", "Mathematics", "Mass Media", "Music", "Civil Engineering", "Hospitality", "Chemistry", "Architecture", "Philosophy", "Drama", "Film Studies", "Anthropology", "Chemical Engineering", "Environmental Science", "Theology", "International Relations", "Physics", "Nutrition Science", "Geology", "Linguistics", "Aerospace Engineering", "Pre-Law", "Biomedical Engineering", "Cognitive Science"];
              
              // #19.  Censored:  Genesis (Band & Bible Book), Jaguar (Mammal), Tesla (Company), Lincoln (State Capital), Toyota (Car Brand), Ram (Mammal), Lotus (Flower)
              cats["Car Brands"] = ["Ford", "Honda", "BMW", "Subaru", "Hyundai", "Audi", "Jeep", "Porsche", "Dodge", "Ferrari", "Lamborghini", "Maserati", "Bentley", "Chrysler", "Cadillac", "Mazda", "Nissan", "Bugatti", "Buick", "Lexus", "Rolls-Royce", "Acura", "Aston Martin", "Chevrolet", "Kia", "Mercedes-Benz", "Volkswagen", "Volvo", "McLaren", "Mitsubishi", "GMC", "Infiniti", "Peugeot", "Pontiac", "Saab", "Suzuki", "Citroen", "Fiat", "Mini", "Peterbilt", "Saturn", "General Motors", "Stellantis", "Land Rover", "Opel"];
              
              // #20.  Censored:  Toyota, Tesla (Car Brands)
              cats["Large Companies"] = ["Apple", "Nvidia", "Microsoft", "Saudi Aramco", "Amazon", "Alphabet", "Meta", "PetroChina", "Broadcom", "TSMC", "Berkshire Hathaway", "Eli Lilly", "Walmart", "JPMorgan Chase", "Visa", "Oracle", "Mastercard", "ExxonMobil", "UnitedHealth", "Costco", "Netflix", "Home Depot", "Procter & Gamble", "Johnson & Johnson", "Bank of America", "SAP", "Salesforce", "ASML", "AbbVie", "Chevron", "Novo Nordisk", "Coca-Cola", "Wells Fargo", "T-Mobile", "Cisco", "Accenture", "Merck", "ServiceNow", "American Express", "Blackstone", "Thermo Fisher", "Morgan Stanley", "Intuitive Surgical", "AstraZeneca", "Linde", "IBM", "Abbott Labs", "GE", "Pepsi"];
              
              // #21.  Censored: Nest (Google Thing), Cloud (Google Thing)
              cats["Collective Nouns"] = ["Flock", "Herd", "Pack", "Pod", "Pride", "Committee", "Colony", "Band", "School", "Congregation", "Army", "Swarm", "Cluster", "Tribe", "Family", "Group", "Troop", "Drove", "Hive", "Flight", "Flutter", "Rabble", "Litter", "Brood", "Murder", "Bunch", "Convocation", "Gang", "Mob", "Flamboyance", "Horde", "Aerie", "Array", "Team", "Parade", "Court", "Loveliness", "Exaltation", "Conspiracy", "Consortium", "Parliament", "Roost", "Prickle", "Bevy", "Brace", "Maelstrom", "Surfeit", "Chattering"];
              
              // #22  Censored:  "C", "R" (Keyboard Characters).  "Julia" (Muppet)  "Ruby" (Gemstone), Swift (Bird)
              cats["Programming Languages"] = ["C++", "Pascal", "Ada", "Fortran", "Python", "Java", "JavaScript", "Lisp", "Go", "C#", "SQL", "PHP", "Perl", "Basic", "MATLAB", "Objective-C", "Rust", "Scratch", "Logo", "COBOL", "Prolog", "Scheme", "Haskell", "TypeScript", "AWK", "Tcl", "Smalltalk", "Algol", "APL", "Bash", "Eiffel", "Forth", "ML", "Modula-2", "OCaml", "Kotlin", "Dart", "Erlang", "Stan", "Lua", "Clojure",
                  "HyperTalk", "PostScript", "Simula", "Delphi"
              ];
              
              // #23.  Censored: Goat (Mammal).
              cats["Cheeses"] = ["Cheddar", "Brie", "Camembert", "Feta", "Gouda", "American", "Mozzarella", "Asiago", "Cream", "Cottage", "Edam", "Emmental", "Monterey Jack", "Colby", "Cotija", "Basket", "Blue", "Burrata", "Fontina", "Gruyere", "Halloumi", "Maasdam", "Manchego", "Mascarpone", "Parmesan", "Swiss", "Gorgonzola", "Havarti", "Jarlsberg", "Limburger", "Muenster", "Neufchatel", "Paneer", "Provolone", "Ricotta", "Romano", "String", "Roquefort", "Epoisses", "Stilton", "Oaxaca", "Bundz", "Canastra", "Wensleydale", "Bocconcini"];
              
              // #24
              cats["Pastas"] = ["Fusilli", "Rigatoni", "Penne", "Bucatini", "Linguine", "Farfalle", "Fettuccine", "Orecchiette", "Spaghetti", "Macaroni", "Pappardelle", "Gnocchi", "Orzo", "Ditalini", "Ziti", "Lasagna", "Tagliatelle", "Angel hair", "Capellini", "Cavatappi", "Gemelli", "Paccheri", "Ravioli", "Cannelloni", "Manicotti", "Rocchetti", "Radiatore", "Rotelle", "Rotini", "Tortellini", "Vermicelli", "Pastina", "Campanelle", "Riccioli", "Lumache", "Strozzapreti", "Calamarata", "Casarecce", "Croxetti", "Fregola", "Busiate", "Pici", "Mafalde", "Conchiglioni", "Cascatelli"];
              
              // #25.  Censored:  Falcon (Bird), Wolverine (Mammal). 
              cats["MCU Character"] = ["Iron Man", "Spider-Man", "Hulk", "Captain America", "Black Widow", "Hawkeye", "Thor", "Nick Fury", "Scarlet Witch", "Vision", "Antman", "Wasp", "Black Panther", "Dr. Strange", "Star-Lord", "Captain Marvel", "Shang-Chi", "Moon Knight", "Ms. Marvel", "Deadpool", "Blade", "America Chavez", "Drax", "Gamora", "Groot", "Heimdall", "Korg", "X-23", "Gambit", "Quicksilver", "Beast", "Daredevil", "Odin", "Colossus", "Human Torch", "Valkyrie", "She-Hulk", "Adam Warlock", "Ironheart", "Professor X", "Rocket Raccoon", "Pepper Potts", "Phil Coulson", "Wong", "Mr. Fantastic", "The Thing", "Human Torch", "Invisible Woman"];
              
              // #26.  Censored:  Genesis (Bible, Car Brand).  Nirvana (Fallacy), Eagles (Bird), Chicago (Musical)
              cats["Rock Bands"] = ["The Beatles", "The Rolling Stones", "The Who", "Led Zeppelin", "Pink Floyd", "AC/DC", "Foo Fighters", "Metallica", "Queen", "Guns N' Roses", "Aerosmith", "U2", "REM", "Black Sabbath", "Pearl Jam", "Fleetwood Mac", "The Doors", "Bon Jovi", "Iron Maiden", "Van Halen", "Ramones", "Kiss", "ZZ Top", "Deep Purple", "Creedence Clearwater Revival", "Motley Crue", "Journey", "Radiohead", "Lynyrd Skynyrd", "Def Leppard", "Rush", "The Beach Boys", "The Allman Brothers", "Grateful Dead", "The Police", "The Clash", "Soundgarden", "Talking Heads", "The Cure", "Steely Dan", "Steve Miller Band", "The Jimi Hendrix Experience", "Coldplay", "ABBA", "Maroon 5"];
              
              // #27.  Censored: Falcon (MCU Charachter), Cardinal (Number), Loon (Google Thing), Swift (Programming Language), Finch (Tom Hanks Movie)
              cats["Birds"] = ["Robin", "Dove", "Mockingbird", "Goldfinch", "Woodpecker", "Sparrow", "Chickadee", "Hummingbird", "Blue Jay", "Titmouse", "Wren", "Magpie", "Duck", "Plover", "Goose", "Swan", "Quail", "Turkey", "Grouse", "Chicken", "Flamingo", "Pigeon", "Roadrunner", "Cuckoo", "Hawk", "Crane", "Snipe", "Puffin", "Penguin", "Gull", "Tern", "Albatross", "Stork", "Gannet", "Booby", "Cormorant", "Condor", "Vulture", "Pelican", "Heron", "Ibis", "Egret", "Osprey", "Eagle", "Owl", "Kingfisher"];
              
              // #28.  Censored: Cloud Atlas (Novel), Bonfire of the Vanities (novel), Greyhound (Dog breed), "extremely loud and incredibly close" (novel), "hologram for a king" (novel)
              cats["Tom Hanks Movies"] = ["Splash", "Bachelor Party", "The Man With One Red Shoe", "Volunteers", "The Money Pit", "Nothing in Common", "Every Time We Say Goodbye", "Dragnet", "Big", "Punchline", "The 'Burbs", "Turner & Hooch", "Joe Versus the Volcano", "A League of their Own", "Sleepless in Seattle", "Philadelphia", "Forrest Gump", "Apollo 13", "Toy Story", "That Thing You Do!", "Saving Private Ryan", "You've Got Mail", "The Green Mile", "Cast Away", "Road to Perdition", "Catch Me If You Can", "The Terminal", "The Polar Express", "The Da Vinci Code", "Charlie Wilson's War", "Angels & Demons", "Larry Crowne", "Captain Phillips", "Saving Mr. Banks", "Bridge of Spies", "Sully", "Inferno", "The Circle", "The Post", "A Beautiful Day in the Neighborhood", "News of the World", "Elvis", "A Man Called Otto", "Asteroid City", "Pinocchio", "Here"];
              
              // #29
              cats["Famous Novels"] = ["The Great Gatsby", "To Kill a Mockingbird", "The Catcher in the Rye", "Ulysses", "Mrs. Dalloway", "Jane Eyre", "Catch-22", "Lord of the Flies", "A Passage to India", "Adventures of Huckleberry Finn", "Wuthering Heights", "Brave New World", "Lolita", "Anna Karenina", "Don Quixote", "The Grapes of Wrath", "On the Road", "Little Women", "David Copperfield", "The Call of the Wild", "Vanity Fair", "One Hundred Years of Solitude", "The Scarlet Letter", "Middlemarch", "Robinson Crusoe", "Invisible Man", "The Sun Also Rises", "Gulliver's Travels", "Beloved", "Madame Bovary", "The Trial", "The Age of Innocence", "Midnight's Children", "Dracula", "Alice's Adventures in Wonderland", "Treasure Island", "Pride and Prejudice", "Moby Dick", "The Pilgrim's Progress", "A Christmas Carol", "A Tale of Two Cities", "Great Expectations", "The Hobbit", "Frankenstein", "Oliver Twist", "Uncle Tom's Cabin", "Crime and Punishment"];
              
              // #30.  Censored:  Dragon (D&D Monster). 
              cats["Trees"] = ["Oak", "Willow", "Spruce", "Beech", "Coconut", "Olive", "Magnolia", "Walnut", "Almond", "Pistachio", "Sequoia", "Sycamore", "Fig", "Cacao", "Redwood", "Fir", "Ash", "Birch", "Clove", "Camphor", "Pine", "Maple", "Aspen", "Cherry", "Chestnut", "Cottonwood", "Elm", "Hawthorn", "Hemlock", "Hickory", "Sassafras", "Hazelnut", "Baobab", "Ginkgo", "Neem", "Rubber", "Papaya", "Pecan", "Banyan", "Brazil nut", "Palm", "Larch", "Cinnamon", "Curry", "Mango"];
              
              // #31.  Censored:  Nirvana (Band)
              cats["Fallacies"] = ["Base rate", "Non sequitur", "Masked-man", "Continuum", "Double counting", "Motte-and-bailey", "Ecological", "False dilemma", "Homunculus", "Lump of labour", "McNamara", "Mind projection", "Moralistic", "Slippery slope", "Begging the question", "No true Scotsman", "Cherry picking", "Post hoc ergo propter hoc", "Gambler's", "Garden of forking paths", "Sunk cost", "Appeal to the stone", "Red herring", "Ad hominem", "Logic chopping", "Ipse dixit", "Chronological snobbery", "Genetic", "Relative privation", "Naturalistic", "Tu quoque", "Texas sharpshooter", "Straw man", "Argumentum ad populum", "Inductive", "Circular reasoning", "Special pleading", "Prosecutor's", "Proof by assertion", "Prevalent proof", "Moving the goalposts", "Ludic", "Intentionality", "If-by-whiskey", "Historical", "False equivalence", "Etymological"];
              
              // #32.  Censored: Nest (Collective Noun)
              cats["Google Products"] = ["Search", "Android", "Calendar", "Chrome", "Drive", "Gmail", "Earth", "Maps", "Meet", "Translate", "Classroom", "Alerts", "Assistant", "Gemini", "Books", "Flights", "Images", "Shopping", "Lens", "News", "Patents", "Scholar", "YouTube", "Ads", "AdMob", "Ad Manager", "Blogger", "FeedBurner", "Chat", "Fonts", "Groups", "Voice", "Charts", "Docs", "Sheets", "Slides", "Drawings", "Forms", "Sites", "Keep", "Analytics", "Street View", "Santa Tracker", "Ngram Viewer", "TensorFlow", "Trends", "Workspace", "App Engine"];
              
              // #33.  Censored: Ruby (Programming Language)
              cats["Gemstones"] = ["Emerald", "Sapphire", "Diamond", "Amethyst", "Opal", "Aquamarine", "Topaz", "Citrine", "Pearl", "Peridot", "Tanzanite", "Garnet", "Alexandrite", "Tourmaline", "Morganite", "Agate", "Moonstone", "Turquoise", "Lapis lazuli", "Pink Sapphire", "Red Coral", "Benitoite", "Beryl", "Jade", "Jasper", "Spinel", "Tsavorite", "Smoky Quartz", "Corundum", "Carnelian", "Fluorite", "Black Onyx", "Rose Quartz", "Malachite", "Tiger's eye", "Apatite", "Chrysocolla", "Amazonite", "Chrysoprase", "Hematite", "Howlite", "Iolite", "Kyanite", "Lepidolite", "Moldavite", "Jadeite"];
              
              // #34.  Censored:  Genesis (Band)
              cats["Books of the Bible"] = ["Exodus", "Leviticus", "Numbers", "Deuteronomy", "Joshua", "Judges", "Ruth", "Samuel", "Kings", "Chronicles", "Ezra", "Nehemiah", "Tobit", "Judith", "Esther", "Maccabees", "Job", "Psalms", "Proverbs", "Ecclesiastes", "Song of Songs", "Wisdom", "Sirach", "Isaiah", "Jeremiah", "Lamentations", "Baruch", "Ezekiel", "Daniel", "Hosea", "Joel", "Amos", "Obadiah", "Jonah", "Micah", "Nahum", "Habakkuk", "Zephaniah", "Haggai", "Zechariah", "Malachi", "Matthew", "Mark", "Luke", "John", "Acts", "Romans", "Corinthians", "Galatians", "Ephesians", "Philippians", "Colossians", "Thessalonians", "Timothy", "Titus", "Philemon", "Hebrews", "James", "Peter", "John", "Jude", "Revelation"];
              
              // #35.  Censored:  Blackbird (name of bird)
              cats["Beatles Songs"] = ["Love Me Do", "Back in the USSR", "Across the Universe", "Yellow Submarine", "Nowhere Man", "And I Love Her", "Dear Prudence", "With a Little Help From My Friends", "Sgt. Pepper's Lonely Hearts Club Band", "I've Just Seen a Face", "Taxman", "Helter Skelter", "Got to Get You Into My Life", "All My Loving", "Drive My Car", "Get Back", "Paperback Writer", "Eight Days a Week", "Penny Lane", "You've Got to Hide Your Love Away", "We Can Work It Out", "Can't Buy Me Love", "Here Comes the Sun", "Happiness is a Warm Gun", "Eleanor Rigby", "All You Need Is Love", "Please Please Me", "Lucy in the Sky With Diamonds", "Tomorrow Never Knows", "Ticket to Ride", "I Saw Her Standing There", "Help!", "Revolution", "Norwegian Wood", "A Hard Day's Night", "While My Guitar Gently Weeps", "Come Together", "Let It Be", "Hey Jude", "Something", "In My Life", "Yesterday", "Strawberry Fields Forever", "I Want to Hold Your Hand", "A Day in the Life"];
              
              // #36.  Censored:  Penciliin (Invention), Bamboo (Almost a Tree), Zombie (D&D Monster)
              cats["Cocktails"] = ["Old Fashioned", "Negroni", "Daiquiri", "Martini", "Margarita", "Whiskey Sour", "Manhattan", "Aperol Spritz", "Mojito", "Bloody Mary", "Gimlet", "Moscow Mule", "Dark 'n' Stormy", "Corpse Reviver", "Clover Club", "Boulevardier", "Mai Tai", "Sazerac", "French 75", "Paloma", "Pisco Sour", "Vieux Carre", "Americano", "Amaretto Sour", "Gin Fizz", "Bramble", "Brandy Crusta", "Bellini", "Pina Colada", "Sidecar", "Aviation", "Irish Coffee", "Last Word", "Tom Collins", "Caiprinha", "Hanky Panky", "Cosmopolitan", "El Diablo", "White Lady", "Gin Gin Mule", "Long Island Iced Tea", "Jungle Bird", "Painkiller", "Fitzgerald", "Old Cuban", "Bee's Knees", "Paper Plane", "Naked & Famous", "Screwdriver", "Kamikaze", "Mint julep", "Mimosa", "Black Russian", "Cuba libre", "Tequila sunrise", "Sex on the beach", "Vesper"];
              
              // #37.  Censored: Lotus (Car Brand)
              cats["Flowers"] = ["Buttercup", "Camellia", "Marigold", "Bluebell", "Violet", "Chrysanthemum", "Edelweiss", "Primrose", "Orchid", "Dahlia", "Rose", "Foxglove", "Hibiscus", "Lavender", "Bird of paradise", "Jasmine", "Lilac", "Delphinium", "Pansy", "Bougainvillea", "Peony", "Daisy", "Azalea", "Periwinkle", "Trillium", "Allium", "Petunia", "Rhododendron", "Phlox", "Nasturtium", "Sunflower", "Baby's breath", "Aster", "Heather", "Tansy", "Carnation", "Tiger lily", "Geranium", "Amaryllis", "Lily", "Yarrow", "Calla lily", "Iris", "Plumeria", "Dandelion", "Daffodil", "Tulip", "Hyacinth"]
              
              // #38
              cats["Fabrics"] = ["Brocade", "Burlap", "Cambric", "Cheesecloth", "Chenille", "Chiffon", "Chintz", "Corduroy", "Crepe", "Damask", "Denim", "Felt", "Fishnet", "Flannel", "Gabardine", "Gauze", "Georgette", "Gingham", "Gore-tex", "Grosgrain", "Hemp", "Herringbone", "Jersey", "Lace", "Lame", "Leather", "Linen", "Loden", "Mackinaw", "Moleskin", "Muslin", "Oilskin", "Organza", "Pleather", "Polyester", "Poplin", "Rayon", "Sailcloth", "Seersucker", "Serge", "Silk", "Spandex", "Taffeta", "Terrycloth", "Tulle", "Tweed", "Twill", "Velour", "Velvet", "Velveteen"];
              
              // #39  Censored: "V" (Keyboard Character)
              cats["Scifi TV Shows"] = ["Quantum Leap", "Babylon 5", "Futurama", "Counterpart", "Orphan Black", "The Twilight Zone", "Firefly", "Fringe", "The Expanse", "Black Mirror", "Stranger Things", "Westworld", "Lost", "Star Trek", "The X-Files", "Doctor Who", "Battlestar Galactica", "Max Headroom", "Farscape", "Rick and Morty", "The 100", "The Prisoner", "Red Dwarf", "Mystery Science Theater 3000", "The Six Million Dollar Man", "Better Off Ted", "Dollhouse", "3rd Rock from the Sun", "Knight Rider", "Lost in Space", "Buck Rogers in the 25th Century", "Stargate SG-1", "The Jetsons", "Mork & Mindy", "Alf", "Foundation", "The Last of Us", "Man in the High Castle", "The Mandalorian", "The Outer Limits", "Torchwood", "Blake's 7", "Sense8", "Eureka", "Space: 1999"];
              
              // #40  Censored: "C", "D", "R", "S" (Programming Language), "V" (Scifi show)
              cats["Keyboard Characters"] = ["A", "B", "E", "F", "G", "H", "I", "J", "K", "L", "M", "N", "O", "P", "Q", "T", "U", "W", "X", "Y", "Z", "0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "`", "-", "=", "ESC", "TAB", "SHIFT", "CTRL", "ALT", "[", "]", "\\", ";", "'", ",", ".", "/", "ENTER"];
              
              // #41
              cats["Weather Words"] = ["Breezy", "Calm", "Cloudy", "Cold", "Drizzle", "Fair", "Fog", "Freezing", "Flurry", "Foggy", "Frost", "Gale", "Gusty", "Hazy", "Humid", "Hail", "Hot", "Hurricane", "Icy", "Lightning", "Muggy", "Misty", "Moist", "Monsoon", "Nor'easter", "Overcast", "Rainy", "Rainbow", "Sleet", "Snowy", "Slush", "Stormy", "Smog", "Squall", "Thunderstorm", "Tornado", "Typhoon", "Windy", "Warm", "Whiteout", "Sunny", "Dry", "Clear", "Blizzard", "Heatwave", "Downpour", "Cool", "Chilly"];
              
              // #42
              cats["Inventions"] = ["Light bulb", "Electricity", "World Wide Web", "Internal combustion engine", "Steam engine", "Compass", "Printing press", "Gunpowder", "Television", "Telephone", "Airplane", "Parachute", "Microwave oven", "Radio", "The Wheel", "The computer", "Bone tools", "Harpoons", "Bow-and-arrow", "Sewing needle", "Shoes", "Mortar and pestle", "Rope", "Ceramics", "Sliced bread", "Fermentation", "Plumbing", "Public sanitation", "Crossbow", "Currency", "Lightning rod", "Catapult", "Paper", "Double-entry bookkeeping", "Toothbrush", "Telescope", "Cotton gin", "Food canning", "Artificial fertilizer", "Pasteurization", "Stainless steel", "Radiocarbon dating", "Global Positioning System", "Cellular phone", "Search engine", "Bitcoin", "mRNA vaccines"];
              
              // #43
              // cats["45"] = [ "vyf-en-veertig", "Ø®Ù…Ø³Ø© ÙˆØ£Ø±Ø¨Ø¹ÙˆÙ†", "à¦ªà¦à¦¯à¦¼à¦¤à¦¾à¦²à§à¦²à¦¿à¦¶", "quaranta-cinc", "å››åäº”", "ÄtyÅ™icet pÄ›t", "femogfyrre", "vijfenveertig", "kvardek kvin", "apatnapu't lima", "neljÃ¤kymmentÃ¤viisi", "quarante-cinq", "fÃ¼nfundvierzig", "ÏƒÎ±ÏÎ¬Î½Ï„Î± Ï€Î­Î½Ï„Îµ", "××¨×‘×¢×™× ×•×—×ž×©", "à¤ªà¥ˆà¤‚à¤¤à¤¾à¤²à¥€à¤¸", "negyvenÃ¶t", "fjÃ¶rutÃ­u og fimm", "empat puluh lima", "daichead a cÃºig", "quarantacinque", "å››åäº”", "ë§ˆí”ë‹¤ì„¯", "quadraginta quinque", "Äetrdesmit pieci", "keturiasdeÅ¡imt penki", "empat puluh lima", "Ä§amsa u erbgÄ§in", "fÃ¸rtifem", "Ú†Ù‡Ù„ Ùˆ Ù¾Ù†Ø¬", "czterdzieÅ›ci piÄ™Ä‡", "quarenta e cinco", "patruzeci È™i cinci", "ÑÐ¾Ñ€Ð¾Ðº Ð¿ÑÑ‚ÑŒ", "dÃ  fhichead 's a cÃ²ig", "Å¡tyridsaÅ¥pÃ¤Å¥", "petinÅ¡tirideset", "cuarenta y cinco", "arobaini na tano", "fyrtiofem", "à¸ªà¸µà¹ˆà¸ªà¸´à¸šà¸«à¹‰à¸²", "kÄ±rk beÅŸ", "ÑÐ¾Ñ€Ð¾Ðº Ð¿'ÑÑ‚ÑŒ", "bá»‘n mÆ°Æ¡i lÄƒm", "pedwar deg pump" ];
              
              // #43 (Replacement).  Censored: Les Miserables (Novel), Chicago (Rock Band)
              cats["Musicals"] = ["Hamilton", "Hadestown", "The Lion King", "Wicked", "Fiddler on the Roof", "The Book of Mormon", "The Sound of Music", "The Phantom of the Opera", "Annie", "Grease", "Hairspray", "Into The Woods", "Little Shop of Horrors", "Mary Poppins", "My Fair Lady", "Cats", "Aladdin", "Mamma Mia", "Jersey Boys", "Beauty and the Beast", "Miss Saigon", "Kinky Boots", "The Producers", "Rent", "Dear Evan Hansen", "Cabaret", "Spider-Man: Turn Off the Dark", "Starlight Express", "Spamalot", "Jesus Christ Superstar", "In the Heights", "West Side Story", "Avenue Q", "Music Man", "Guys and Dolls", "Six", "Moulin Rouge", "Mean Girls", "Oklahoma!", "Sweeney Todd", "Man of La Mancha", "Oliver!", "Chess", "South Pacific", "The King and I"];
              
              // #44.  Censored: Public Health (College Major)
              // cats["Medical Specialties"] = ["Surgery", "Internal medicine", "Pediatrics", "Emergency medicine", "Anesthesiology", "Neurology", "Family medicine", "Obstetrics", "Gynaecology", "Psychiatry", "Pathology", "Ophthalmology", "Immunology", "Dermatology", "Urology", "Cardiology", "Radiology", "Orthopedics", "Gastroenterology", "Oncology", "Rheumatology", "Nephrology", "Hematology", "Sleep medicine", "Pulmonology", "Endocrinology", "Forensic pathology", "Geriatrics", "Podiatry", "Infectious diseases", "Medical genetics", "Otolaryngology", "Plastic surgery", "Sports medicine", "Thoracic Surgery", "Medical Toxicology", "Nuclear medicine", "Preventive medicine", "Physical medicine", "Trauma Surgery", "Cardiothoracic Surgery", "Vascular Surgery", "Neonatology", "Pediatric Intensivist", "Neurosurgery"];
              
              // #45
              cats["Legal Doctrines"] = ["Abatement ab initio", "Abstention", "Act of state", "Attractive nuisance", "Aggregate effects", "Assignment of income", "Blue pencil", "Born secret", "Caveat emptor", "De minimis", "Cash equivalence", "Privity", "Res judicata", "Eggshell skull", "Erie", "Estoppel", "Ex turpi causa non oritur actio", "Exhaustion of remedies", "Fair use", "First-sale", "Forum non conveniens", "Ignorantia juris non excusat", "Major questions", "Last clear chance", "Ker-Frisbie", "Internal affairs", "Chances", "Colourability", "Equivalents", "Exoneration of Liens", "Foreign Equivalents", "Indivisibility", "Inherency", "International Exhaustion", "Discovery", "Dangerous proximity", "Correlative rights", "Laches", "Merger", "Necessity", "Repair and reconstruction", "Tenure", "Stare decisis", "Res Ipsa Loquitur", "Comparative Negligence", "Unconscionability", "Ancillary Powers", "Pleasure", "Territorial Nexus", "Contra Proferentem", "Consideration"];
              
              // Replacement for #44.
              cats["Poets"] = ["Robert Frost", "E. E. Cummings", "T. S. Eliot", "Dante", "Homer", "Maya Angelou", "Emily Dickinson", "Walt Whitman", "Edgar Allan Poe", "William Shakespeare", "Sylvia Plath", "Pablo Neruda", "Langston Hughes", "William Blake", "William Wordsworth", "John Keats", "William Butler Yeats", "Elizabeth Barrett Browning", "Rudyard Kipling", "Oscar Wilde", "Henry Longfellow", "John Milton", "Sappho", "Lord Byron", "Percy Shelly", "Alfred Tennyson", "Ralph Waldo Emerson", "W. H. Auden", "Robert Burns", "Allen Ginsburg", "Ogden Nash", "Phillip Larkin", "Khalil Gibran", "Rainer Maria Rilke", "Dorothy Parker", "Chaucer", "Virgil", "Edmund Spenser", "John Donne", "Alexander Pope", "Samuel Coleridge", "Robert Browning", "Wallace Stevens", "John Dryden", "Ezra Pound", "Thomas Hardy", "John Ashbery"];
       
            // Functions
            
            function deselect() {
              if (selected != 0) {
                selected.style = "";
                selected = 0;
                document.getElementById("deselect").disabled = true;
              }
            }
            
            function stringToLightColor(str) {
              let hash = 0;
              for (let i = 0; i < str.length; i++) {
                hash = str.charCodeAt(i) + ((hash << 5) - hash);
              }

              const h = Math.abs(hash) % 360;
              const s = 70;
              const l = 80 + (Math.abs(hash) % 10); 

              const lDev = l / 100;
              const a = (s * Math.min(lDev, 1 - lDev)) / 100;
  
              const f = (n) => {
                const k = (n + h / 30) % 12;
                const color = lDev - a * Math.max(Math.min(k - 3, 9 - k, 1), -1);
                return Math.round(255 * color).toString(16).padStart(2, "0");
              };

              return `#${f(0)}${f(8)}${f(4)}`;
            }
            
            function getRandomHexColor() {
              return '#' + Math.floor(Math.random() * 16777215).toString(16);
            }
            
            function finishCategory(b) {
              b.innerHTML  =  '<b>' + b.category + '</b>';
              b.disabled = true;
              b.style.background= stringToLightColor(b.category);
            }
            
            function checkCategories() {
              const wordDict = new Map();
              // Check that each category has 45 entries.
              for (const [key, value] of Object.entries(cats)) {
                if (value.length < 45) {
                  alert(`Entry for ${key} has length ${value.length}`);
                }
                for (let i = 0; i < 45; i++) {
                  wordlist.push([value[i], key]);
                  if (wordDict.has(value[i])) {
                      alert(`Duplicate word ${value[i]}`);
                  }
                  else {
                      wordDict.set(value[i], true);
                  }
                }
              }
            }
            
            function shuffleArray(array) {
              for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
              }
            }
            
            function saveState() {
                localStorage.clear();
                
                localStorage.setItem('score', score + '');
                localStorage.setItem('mistakes', mistakes + '');
                
                var t = document.getElementById('the_table');
                for (let i = 0; i < t.rows.length; i++) {
                  let row = t.rows[i];
                  for (let j = 0; j < row.cells.length; j++) {
                    let cell = row.cells[j];
                    let button = cell.firstChild;
                    localStorage.setItem(`${i}_${j}_category`, button.category);
                    localStorage.setItem(`${i}_${j}_cluster`, JSON.stringify(button.cluster));
                    localStorage.setItem(`${i}_${j}_innerHTML`, button.innerHTML);
                    localStorage.setItem(`${i}_${j}_title`, button.title);
                  }
                }
                
            }
            
            function putWordsInBoard() {
              shuffleArray(wordlist);
              var currentWordIndex = 0;
              var table = document.getElementById("the_table");
              for (let i = 0, row; row = table.rows[i]; i++) {
                for (let j = 0, col; col = row.cells[j]; j++) {
                  var button = col.firstElementChild;
                  // button.pos = currentWordIndex;
                  button.textContent = wordlist[currentWordIndex][0];
                  button.category = wordlist[currentWordIndex][1];
                  button.cluster = [button.textContent];
                  currentWordIndex += 1;
                }
              }
            }
            
            function startFireworks() {
              const canvas = document.getElementById('fireworks');
              const ctx = canvas.getContext('2d');
              let w, h;
              let particles = [];
              let fireworks = [];

              function resize() {
                w = canvas.width = window.innerWidth;
                h = canvas.height = window.innerHeight;
              }
              window.addEventListener('resize', resize);
              resize();

              class Firework {
                constructor() {
                    this.x = Math.random() * w;
                    this.y = h;
                    this.tx = Math.random() * w;
                    this.ty = Math.random() * (h / 2);
                    this.color = `hsl(${Math.random() * 360}, 100%, 50%)`;
                    this.speed = 2 + Math.random() * 2;
                    this.angle = Math.atan2(this.ty - this.y, this.tx - this.x);
                    this.vx = Math.cos(this.angle) * this.speed;
                    this.vy = Math.sin(this.angle) * this.speed;
                    this.exploded = false;
                }

                update() {
                    this.x += this.vx;
                    this.y += this.vy;
                    if (this.vy < 0 && this.y < this.ty) {
                        this.explode();
                    } else if (this.vy > 0 && this.y > this.ty) { // Should technically not happen with simple upward movement
                         this.explode();
                    }
                }

                explode() {
                    this.exploded = true;
                    for (let i = 0; i < 50; i++) {
                        particles.push(new Particle(this.x, this.y, this.color));
                    }
                }

                draw() {
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, 3, 0, Math.PI * 2);
                    ctx.fillStyle = this.color;
                    ctx.fill();
                }
              }

              class Particle {
                constructor(x, y, color) {
                    this.x = x;
                    this.y = y;
                    this.color = color;
                    const angle = Math.random() * Math.PI * 2;
                    const speed = Math.random() * 3;
                    this.vx = Math.cos(angle) * speed;
                    this.vy = Math.sin(angle) * speed;
                    this.alpha = 1;
                    this.decay = Math.random() * 0.015 + 0.005;
                }

                update() {
                    this.x += this.vx;
                    this.y += this.vy;
                    this.vy += 0.05; // gravity
                    this.alpha -= this.decay;
                }

                draw() {
                    ctx.save();
                    ctx.globalAlpha = this.alpha;
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, 2, 0, Math.PI * 2);
                    ctx.fillStyle = this.color;
                    ctx.fill();
                    ctx.restore();
                }
              }

              function loop() {
                // Clear with a trail effect
                ctx.globalCompositeOperation = 'destination-out';
                ctx.fillStyle = 'rgba(0, 0, 0, 0.1)'; 
                ctx.fillRect(0, 0, w, h);
                ctx.globalCompositeOperation = 'lighter';

                // Launch new firework randomly
                if (Math.random() < 0.05) {
                    fireworks.push(new Firework());
                }

                // Update and draw fireworks
                for (let i = fireworks.length - 1; i >= 0; i--) {
                    fireworks[i].update();
                    fireworks[i].draw();
                    if (fireworks[i].exploded) {
                        fireworks.splice(i, 1);
                    }
                }

                // Update and draw particles
                for (let i = particles.length - 1; i >= 0; i--) {
                    particles[i].update();
                    particles[i].draw();
                    if (particles[i].alpha <= 0) {
                        particles.splice(i, 1);
                    }
                }

                requestAnimationFrame(loop);
              }

              loop();
            }
            
            function setUpBoard() {
              // Just sets up the dom elements; does not put words in them.
              var b = document.getElementById("board");   
              const table = document.createElement('table');
              table.id = 'the_table';
              for (let i = 0; i < M; i++) {
                const tr = document.createElement('tr');
                tr.classList.add('.row');
                for (let j = 0; j < M; j++) {
                   const td = document.createElement('td');
                   td.class = "bigbut";
                   const button = document.createElement('button');
                   button.class = "bigbut"
                   button.onclick = function() {
                       if (selected == button) {
                         deselect();
                         return;
                       }
                       button.style = "filter: invert(100%)";
                       
                       if (selected == 0) {
                           selected = button;
                           document.getElementById("deselect").disabled = false;
                           return;
                       }
                       
                       document.getElementById("deselect").disabled = true;

                       if (button.category == selected.category) {
                         
                         firstbut = button;
                         secondbut = selected;
                         
                         // Deselect
                         selected.style = "";
                         button.style = "";
                         selected = 0;
                         score = score + 1;
                         
                         // Merge
                         firstbut.cluster = firstbut.cluster.concat(secondbut.cluster);
                         // console.log(firstbut.cluster);
                         if (firstbut.cluster.length == 2) {
                           firstbut.innerHTML = '<b>' + firstbut.cluster.join('; ') + '</b>';
                         } else {
                           firstbut.innerHTML = '<b>' + firstbut.cluster.slice(0, 2).join(', ') + ', ... <span class="red">[' + firstbut.cluster.length + ']</span></b>';
                           firstbut.title = firstbut.cluster.join('\n');
                         }
                         if (firstbut.cluster.length == 45) {
                             finishCategory(firstbut);
                         }
                         
                         document.getElementById("score").textContent = score;
                         // Remove the button for the eliminated word.
                         secondbut.title = '';
                         // if (currentWordIndex < wordlist.length) {
                         //  secondbut.textContent = wordlist[currentWordIndex][0];
                         //  secondbut.category = wordlist[currentWordIndex][1];
                         // secondbut.cluster = [secondbut.textContent];
                         //  currentWordIndex += 1;
                         // } else {
                             secondbut.textContent = '';
                             secondbut.disabled = true;
                             
                             // Remove not just the button but the entire <td>.
                             secondbut.parentElement.remove();
                        // }
                         
                       } else {
                         mistakes = mistakes + 1;
                         document.getElementById("mistakes").textContent = mistakes;
                         // Shake it
                         button.classList.add('shake');
                         selected.classList.add('shake');
                         
                         button.addEventListener('animationend', () => {
                           button.classList.remove('shake');
                           button.style = "";
                         }, { once: true });
  
                         old_selected = selected;
                         old_selected.addEventListener('animationend', () => {
                           old_selected.classList.remove('shake');
                           old_selected.style = "";
                         }, { once: true });
  
                         selected = 0;
                       }
                       saveState();
                       if (score == 1980) {
                           window.alert("You win!!");
                           startFireworks();
                       }
                   };
                   td.appendChild(button);
                   tr.appendChild(td);
                }
                table.appendChild(tr);
              }
              
              document.getElementById("board").appendChild(table);
            }
            
            function loadState() {
                s = localStorage.getItem('score');
                if (s == null) {
                    putWordsInBoard();
                    return;
                }
                score = Number(s);
                document.getElementById("score").textContent = score;
                
                s = localStorage.getItem('mistakes');
                if (s == null) {
                    putWordsInBoard();
                    return;
                }
                mistakes = Number(s);
                document.getElementById("mistakes").textContent = mistakes;
                
                var currentWordIndex = 0;
                var table = document.getElementById("the_table");
                var cells_to_remove = [];
                for (let i = 0, row; row = table.rows[i]; i++) {
                  for (let j = 0, col; col = row.cells[j]; j++) {
                    var button = col.firstElementChild;
                    // button.pos = currentWordIndex;
                    button.category = localStorage.getItem(`${i}_${j}_category`);
                    button.cluster = JSON.parse(localStorage.getItem(`${i}_${j}_cluster`));
                    if (Array.isArray(button.cluster) && button.cluster.length == 45) {
                        finishCategory(button);
                    }
                    button.innerHTML = localStorage.getItem(`${i}_${j}_innerHTML`);
                    button.title = localStorage.getItem(`${i}_${j}_title`);
                    currentWordIndex += 1;
                    if (button.innerHTML == '') {
                        // No word here, remove the whole <td>
                        cells_to_remove.push(col);
                    }
                  }
                }
                for (let i = 0; i < cells_to_remove.length; i++) {
                    cells_to_remove[i].remove();
                }
            }
            
            checkCategories();
            setUpBoard();
            loadState();
        </script>

</html>
