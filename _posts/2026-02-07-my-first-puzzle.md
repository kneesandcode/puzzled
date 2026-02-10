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
            cats["Cat Breeds"] = ["Abyssinian", "American bobtail", "American wirehair", "Aphrodite Giant", "Arabian Mau", "Asian semi-longhair", "Australian Mist", "Balinese", "Bambino", "Bengal", "Birman", "Burmese", "Burmilla", "California Spangled", "Chantilly-Tiffany", "Chausie", "Colorpoint Shorthair", "Cornish Rex", "Cymric", "Devon Rex", "Donskoy", "York Chocolate", "Foldex", "Havana Brown", "Highlander", "Javanese", "Kanaani", "Khao Manee", "Kinkalow", "Korat", "Lambkin", "Turkish Angora", "Lykoi", "Maine Coon", "Thai Lilac", "Neva Masquerade", "Oriental Bicolor", "Ragdoll", "Russian Blue", "Savannah", "Scottish Fold", "Serengeti", "Siamese", "Snowshoe", "Sokoke"];
            
            // #3.  Censored: 
            cats["German Loanwords"] = ["Bratwurst", "Delicatessen", "Frankfurter", "Kohlrabi", "Lager", "Muesli", "Noodle", "Pumpernickel", "Quark", "Strudel", "Abseil", "Blitz", "Karabiner", "Rucksack", "Dachshund", "Hamster", "Poodle", "Rottweiler", "Neanderthal", "Schadenfreude", "Wanderlust", "Zeitgeist", "Doppelgänger", "Kindergarten", "Kitsch", "Poltergeist", "Wunderkind", "Ersatz", "Flak", "Zeppelin", "Kaput", "Nix", "Verboten", "Gestalt", "Glockenspiel", "Leitmotif", "Sitzprobe", "Verfremsdungseffekt", "Zugzwang", "Hinterland", "Karst", "Bildungsroman", "Putsch", "Weltanschauung", "Realpolitik"];

            // #4.  Censored: 
            cats["National Trust Properties Seen on Screen"] = ["Botallack", "Holywell", "St Michael's Mount", "Antony", "Greenway", "Saltram", "Montacute House", "Barrington Court", "Tyntesfield", "Brean Down", "Great Chalfield Manor", "Lacock Abbey", "Stourhead", "Dyrham Park", "Woodchester Park", "Chastleton House", "Basildon Park", "Cliveden", "Stowe", "Claydon", "Ashridge Estate", "Frensham Little Pond", "Petworth", "Lamb House", "Scotney Castle", "Chartwell", "Knole", "Fenton House", "Osterley Park", "Ham House", "Wimpole Estate", "Lavenham Guildhall", "Felbrigg Hall", "Packwood House", "Benthall Hall", "Belton House", "Hardwick", "Freshwater West and Gupton Farm", "Dyffryn Gardens", "Tredegar House", "Henrhyd Falls", "Bodanant Garden", "Penrhyn Castle", "Castle Ward", "Fountains Abbey and Studley Water Garden"];
            
            // #5
            cats["JMW Turner Paintings"] = ["The Fighting Temeraire", "Rain, Steam and Speed", "The Slave Ship", "Snow Storm: Steam-Boat off a Harbour's Mouth", "The Burning of the Houses of Lords and Commons", "Fishermen at Sea", "Dido Building Carthage", "Norham Castle, Sunrise", "The Shipwreck", "Calais Pier", "Light and Colour (Goethe's Theory)", "Frosty Morning", "Venice from the Porch of Madonna della Salute", "Sun Rising through Vapour", "Ulysses Deriding Polyphemus", "London from Greenwich Park", "The Battle of Trafalgar, as seen from the Mizen Starboard Shrouds of the Victory", "Rome, from Mount Aventine", "Chichester Canal", "The Fifth Plague of Egypt", "Modern Rome - Campo Vaccino", "Regulus", "Rome, from the Vatican", "Snow Storm: Hannibal and his Army Crossing the Alps", "The Rigi", "The Scarlet Sunset", "Venice from the Giudecca", "Helvoetsluys", "The Rising Squall, Hot Wells", "The Harbour of Dieppe", "Keelmen Heaving in Coals by Moonlight", "Yacht Approaching the Coast", "Three Seascapes", "Margate", "The Angel Standing in the Sun", "Crossing the Brook", "Peace - Burial at Sea", "Sunrise with Sea Monsters", "Sun Setting over a Lake", "Mortlake Terrace: Early Summer Morning", "Fort Vimieux", "Dutch Boats in a Gale", "The Field of Waterloo", "Dort or Dordrecht: The Dort Packet from Rotterdam Becalmed", "Sheerness as Seen from the Nore"];

            // #6
            cats["Oxford Colleges"] = ["Balliol", "Blackfriars", "Brasenose", "Campion", "Christ Church", "Corpus Christi", "Exeter", "Green Templeton", "Harris Manchester", "Hertford", "Jesus", "Keble", "Kellogg", "Lady Margaret Hall", "Linacre", "Lincoln", "Magdalen", "Mansfield", "Merton", "New", "Nuffield", "Oriel", "Pembroke", "Regent's Park", "Reuben", "Somerville", "St Anne's", "St Antony's", "St Catherine's", "St Cross", "St Edmund", "St Hilda's", "St Hugh's", "St John's", "St Peter's", "The Queen's", "Trinity", "University", "Wadham", "Wolfson", "Worcester", "Wycliffe", "Ripon College Cuddesdon", "St Stephen's House", "St Hilda's (again)"];

             // #7.  Censored: 
             cats["Ralph Fiennes Films"] = ["28 Years Later", "The Choral", "The Return", "Conclave", "The Wonderful Story of Henry Sugar", "The Menu", "The King's Man", "No Time to Die", "The Dig", "Official Secrets", "Holmes & Watson", "The White Crow", "Sea Sorrow", "The Lego Batman Movie", "Hail, Caesar!", "Spectre", "A Bigger Splash", "Two Women", "The Grand Budapest Hotel", "The Invisible Woman", "Great Expectations", "Wrath of the Titans", "Coriolanus", "Harry Potter and the Order of the Phoenix", "Cemetery Junction", "Nanny McPhee Returns", "The Reader", "The Duchess", "In Bruges", "The Constant Gardener", "Maid in Manhattan", "Red Dragon", "Spider", "The End of the Affair", "Onegin", "The Prince of Egypt", "The Avengers", "Oscar and Lucinda", "The English Patient", "Strange Days", "Quiz Show", "Schindler's List", "Wuthering Heights", "The Baby of Mâcon", "Sunshine"];
             
             // #8.  
             cats["Famous Cats (real and fictional)"] = ["Félicette", "Tuxedo Stan", "Bobby Kertanegara", "Paddles", "Luffar-Lasse", "Beerbohm", "Catmando", "Crimean Tom", "Hamish McHamish", "Tibs the Great", "Unsinkable Sam", "Socks Clinton", "Jorts and Jean", "Grumpy Cat", "Lil Bub", "Maru", "Longcat", "Sockington", "Tsim Tung Brother Cream", "Colonel Meow", "Prince Chunk", "Mrs Chippy", "Choupette", "Nedjem", "Muezza", "Cheshire Cat", "Nelson", "Simpkin", "Tiddles", "Misty Malarky Ying Yang", "Dusty the Klepto Kitty", "Little Nicky", "Smudge", "Palmerston", "Garfield", "Mademoiselle Fifi", "Salem", "Wilberforce", "Jonesy", "Mr Bigglesworth", "Crookshanks", "Mrs Norris", "Pyewacket", "Zunar-J5", "Thackery Binx"];


              // #9.  Censored:  
              cats["Crime Fiction Writers"] = ["Boris Akunin", "Kate Atkinson", "Mark Billingham", "John Dickson Carr", "Raymond Chandler", "GK Chesterton", "Lee Child", "Agatha Christie", "Mary Higgins Clark", "Harlan Coben", "Patricia Cornwell", "Colin Dexter", "Arthur Conan Doyle", "Umberto Eco", "Antonia Fraser", "Tana French", "Dashiell Hammett", "Patricia Highsmith", "Reginald Hill", "Arnaldur Indriðason", "PD James", "Stieg Larsson", "Mick Herron", "John le Carré", "Donna Leon", "Gaston Leroux", "Henning Mankell", "Alexander McCall Smith", "Val McDermid", "Denise Mina", "Jo Nesbø ", "Sara Paretsky", "Ellis Peters", "Edgar Allan Poe", "Ellery Queen", "Ian Rankin", "Kathy Reichs", "Ruth Rendell", "Peter Robinson", "CJ Sansom", "Karin Slaughter", "Martin Cruz Smith", "Andrew Taylor", "Dorothy L Sayers", "Josephine Tey"];
              
              // #10
              cats["Latin Names of Plants and Flowers"] = ["Agapanthus", "Laurus nobilis", "Rudbeckia", "Rubus", "Buddleia", "Helleborus niger", "Echinacea", "Geranium", "Helichrysum", "Anthriscus", "Hemerocallis", "Bergenia", "Ribes", "Digitalis", "Forsythia", "Ulex", "Aquilegia", "Eucalyptus", "Calluna", "Ilex", "Lonicera", "Jasminum", "Lavandula", "Syringa", "Lavatera", "Vinca", "Hosta", "Papaver", "Kniphofia", "Cistus", "Perovskia", "Salvia", "Hebe", "Astelia", "Galanthus", "Gladiolus", "Trachelospermum", "Euphorbia", "Fiscus", "Hamamelis", "Ranunculus acris", "Bellis perennis", "Taraxacum originale", "Viola arvensis", "Primula"];
              
              // #11 Censored: 
              cats["Sewing and Knitting Terms"] = ["Blocking", "Cast off", "Cable", "Entrelac", "Fair Isle", "Intarsia", "Jog", "Mosaic", "Leading leg", "Fisherman's rib", "Steeking", "Frogging", "Brioche", "Purl", "Slip", "Garter", "Selvedge", "Stockinette", "Skein", "Binding", "Boning", "Bumblebunching", "Charm square", "Grain", "Interlocking", "Ladder", "Nap", "Notches", "Darts", "Notions", "Pinking", "Ripping", "Ruffler", "Shirring", "Smocking", "Tacking", "Bobbin", "Warp", "Weft", "Zip", "Hem", "Seam", "Felting", "Feed-dogs", "Appliqué"];
              
              // #12.  Censored:  
              cats["The Archers Characters"] = ["Jill Archer", "Christine Barford", "Leonard Berry", "Clive Horrobin", "Matt Crawford", "Lilian Bellamy", "Shula Hebden Lloyd", "Caroline Bone", "Kenton Archer", "Elizabeth Pargetter", "Helen Titchener", "Brian Aldridge", "Brenda Tucker", "Robin Fairbrother", "Rúairi Donovan", "Clarrie Grundy", "Barbarella", "Emma Carter", "Oliver Sterling", "Siobhán Hathaway", "Adam Macy", "Paddy Redmond", "Simon Gerrard", "Rob Titchener", "Lucas Madikane", "Tracey Horrobin", "Roy Tucker", "Nigel Pargetter", "Sergeant Harrison Burns", "Ian Craig", "Alan Franks", "Usha Gupta", "Richard Locke", "Jack 'Jazzer' McCreary", "Kirsty Miller", "Stella Pryor", "Fallon Rogers", "Lynda Snell", "Zebedee Tring", "Carol Tregorran", "Marjorie Antrobus", "Bert Fry", "Nelson Gabriel", "Sid Perks", "Polly Perkins"];
              
              // #13.  Censored: 
              cats["Buckinghamshire Villages"] = ["Barton Hartshorn", "Bellingdon", "Boarstall", "Bow Brickhill", "Lillingstone Lovell", "Cadmore", "Castlethorpe", "Chartridge", "Cholesbury", "Clifton Reynes", "Cublington", "Dagnall", "Drayton Beauchamp", "Edgcott", "Emberton", "Fawley Bottom", "Fingest", "Fulmer", "Gawcott", "Great Horwood", "Grendon Underwood", "Haddenham", "Hambleden", "Hazlemere", "Hoggeston", "Hyde Heath", "Iver", "Jordans", "Kingsey", "Leckhampstead", "Ley Hill", "Marsh Gibbon", "Nether Winchendon", "Newton Blossomville", "Oving", "Padbury", "Quainton", "Ravenstone", "Shabbington", "Speen", "Tylers Green", "Stoke Poges", "Taplow", "Pitchcott", "Mentmore"];
              
              // #14.  Censored:  
              cats["Commonly Misspelled Words, Misspelled"] = ["accomodate", "adress", "adviseable", "agressive", "anually", "becuase", "bouy", "cemetary", "commited", "conceed", "definately", "diffrence", "embarass", "fourty", "independant", "jewelery", "milennium", "miniscule", "neccessary", "occassion", "occured", "possesion", "pronounciation", "questionaire", "recieve", "restaraunt", "rythm", "seperate", "succesful", "suprise", "tommorrow", "untill", "wierd", "withold", "dosen't", "desparate", "dissapoint", "gratefull", "humerous", "inteligence", "momento", "mischevious", "ommision", "privelege", "tomatos"];
              
              // #15
              cats["Most Popular Names for Girls Born in 1950s"] = ["Susan", "Linda", "Christine", "Margaret", "Janet", "Patricia", "Carol", "Elizabeth", "Mary", "Anne", "Ann", "Jane", "Jacqueline", "Barbara", "Sandra", "Gillian", "Pauline", "Elaine", "Lesley", "Angela", "Pamela", "Helen", "Jennifer", "Valerie", "Jean", "Catherine", "Julie", "Kathleen", "Denise", "Maureen", "Judith", "Wendy", "Diane", "Sheila", "Janice", "Lynn", "Karen", "Yvonne", "Shirley", "Marion", "Maria", "Rosemary", "Carole", "Joan", "Marilyn"];
              
              // #16
              cats["Children's TV Programmes 1960s-80s"] = ["Andy Pandy", "The Adventures of Black Beauty", "Bagpuss", "Captain Pugwash", "Catweazle", "Clangers", "Crackerjack!", "Hector's House", "Ivor the Engine", "Jackanory", "The Magic Roundabout", "Magpie", "Mr Benn", "Mr. Men", "Multi-Coloured Swap Shop", "Blue Peter", "Pinky and Perky", "Flower Pot Men", "Rainbow", "Rentaghost", "Roobarb", "Tiswas", "The Woodentops", "What-a-Mess", "The Wombles", "Worzel Gummidge", "The Adventures of Rupert the Bear", "Bleep and Booster", "Camberwick Green", "Captain Scarlet and the Mysterons", "Fireball XL5", "The Herbs", "Joe 90", "Noggin the Nog", "Rag, Tag and Bobtail", "Trumpton", "Muffin the Mule", "The Sooty Show", "Grange Hill", "Animal Kwackers", "DoDo, The Kid from Outer Space", "Pipkins", "Tales of the Riverbank", "The Tomorrow People", "Why Don't You?"];
              
              // #17
              cats["Teas of the World"] = ["Assam", "Ceylon", "Darjeeling", "Golden Monkey", "Sikkim", "Oolong", "Lapsang Souchong", "Lu'an Melon Seed", "Gunpowder", "Matcha", "Sencha", "White Peony", "Silver Needle", "Pu-erh", "Earl Grey", "English Breakfast", "Jasmine", "Masala Chai", "Prince of Wales", "Russian Caravan", "Bohea", "Congou", "Dianhong", "Kangra", "Keemun", "Nilgiri", "Tibeti", "Rize", "Yingdehong", "Dong ding", "Dongfang meiren", "Qilan", "Rougui", "Iron Luohan", "Shui Xian", "Bancha", "Dafang", "Lahpet", "Bu Zhi Chun", "Aracha", "Chun Mee", "Gyokuro", "Hyson", "Mecha", "Junshan Yinzhen"];
              
              // #18.  Censored:  
              cats["Nation's Favourite Poems"] = ["If-", "The Lady of Shalott", "The Listeners", "Not Waving but Drowning", "The Daffodils", "To Autumn", "The Lake Isle of Inisfree", "Dulce et Decorum Est", "Ode to a Nightingale", "He Wishes for the Cloths of Heaven", "Remember", "Elegy Written in a Country Churchyard", "Fern Hill", "Leisure", "The Highwayman", "To His Coy Mistress", "Dover Beach", "The Tyger", "Twelve Songs IX", "Adlestrop", "The Soldier", "Warning", "Sea-Fever", "Upon Westminster Bridge", "Sonnets from the Portuguese XLIII", "The Love Song of J Alfred Prufrock", "Cargoes", "Jabberwocky", "from The Rime of the Ancient Mariner Part I", "Ozymandias of Egypt", "Stopping by Woods on a Snowy Evening", "Abou Ben Adhem", "Everyone Sang", "The Windhover", "Do Not Go Gentle into That Good Night", "Sonnet 18", "When You Are Old", "The Darkling Thrush", "Please Mrs Butler", "Kubla Khan", "Home-Thoughts, from Abroad", "High Flight (An Airman's Ecstasy)", "Journey of the Magi", "The Owl and the Pussy-Cat", "The Road Not Taken"];
              
              // #19.  Censored:  
              cats["Cherry Varieties"] = ["Alba Heart", "Bigarreau Napoleon", "Black Oliver", "Bradbourne Black", "Circassian", "Crooked Billet", "Early Purple Guigne", "Early Rivers", "Empress Eugenie", "Frogmore Early", "Goblin", "Hertfordshire Black", "May Duke", "Merton Glory", "Morello", "Nimble Dick", "Noir De Guben", "Nutberry Black", "Prestwood White Heart", "Ruth Tolman", "Smoky Dun", "Strang Loggie", "Yellow Bigarreau", "Yellow Spanish", "Black Tartarian", "Burcombe", "Buttners Spate Rote Knorpelkirsche", "Colney", "Della Recca", "Ebony Pearl", "Flamentiner", "Garden Bing", "Hedelfingen", "Ironsides", "Jubileum", "Kordia", "Montmorency", "Nimba", "Ron's Seedling", "Solymari Gombolyu", "Tragana Edessis", "Yates Seedling", "Starkrimson", "Starking Hardy Giant", "Sutton's Prolific"];
              
              // #20.  Censored:  
              cats["Contestants on The Traitors UK"] = ["Alyssa Chan", "Amanda Lovett", "Wilfred Webster", "Kieran Tompsett", "Ash Bibi", "Harry Clark", "Paul Gorton", "Miles Asteri", "Andrew Jenkins", "Ross Carson", "Armani Gouveia", "Linda Rands", "Minah Shannon", "Charlotte Berman", "Freddie Fraser", "Alan Carr", "Cat Burns", "Jonathan Ross", "Fiona Hughes", "Hugo Lodge", "Rachel Duffy", "Stephen Libby", "Aaron Evans", "Hannah Byczkowski", "Meryl Williams", "Jake Brown", "Leanne Quigley", "Mollie Pearce", "Jaz Singh", "Evie Morrison", "Faraaz Noor", "Francesca Rowden-Plowden", "Alexander Dragonetti", "Jade Scott", "Jack Butler", "James Baker", "Madelyn Smedley", "Jasmine Boatswain", "Roxy Wilson", "Fay Greaves", "Diane Carson", "Matthew Hyndman", "Joe Scott", "Ellie Buckley", "Jessie Stride"];
              
              // #21.  
              cats["Phrases Coined By Shakespeare"] = ["Be cruel to be kind", "Green-eyed monster", "Wear your heart on your sleeve", "The world's your oyster", "Break the ice", "Heart of gold", "What the dickens", "(To be) in stitches", "Apple of my eye", "Foregone conclusion", "Faint-hearted", "High time", "Laughing stock", "More fool you", "Night owl", "Short shrift", "Wild goose chase", "With bated breath", "Brave new world", "Good riddance", "In a pickle", "It's all Greek to me", "Star-crossed lovers", "Dead as a doornail", "Fair play", "Eaten out of house and home", "Hoisted by his own petard", "In my heart of hearts", "To kill with kindness", "Lie low", "Melted into thin air", "Salad days", "My own flesh and blood", "Sound and fury", "Sterner stuff", "The be-all and end-all", "Thereby hangs a tale", "The lady doth protest too much", "A method in my madness", "Too much of a good thing", "Tower of strength", "A blinking idiot", "All that glitters is not gold", "Jaws of death", "Love is blind"];
              
              // #22  Censored:  
              cats["Strictly Come Dancing Professionals"] = ["Brendan Cole", "Anton Du Beke", "Paul Killick", "Camilla Dallerup", "Erin Boag", "Nicole Cutler", "Ian Waite", "Lilia Kopylova", "Darren Bennett", "Matthew Cutler", "Karen Hardy", "Flavia Cacace", "Ola Jordan", "Vincent Simone", "James Jordan", "Kristina Rihanoff", "Brian Fortuna", "Aliona Vilani", "Katya Virshilas", "Natalie Lowe", "Robin Windsor", "Artem Chigvintsev", "Pasha Kovalev", "Karen Hauer", "Iveta Lukošiūtė", "Kevin Cilfton", "Janette Manrara", "Aljaž Škorjanec", "Joanne Clifton", "Tristan McManus", "Oti Mabuse", "Gleb Savchenko", "Giovanni Pernice", "Gorka Márquez", "Nadiya Bychkova", "Dianne Buswell", "Johannes Radebe", "Nikita Kuzmin", "Nancy Xu", "Kai Widdrington", "Jowita Przystał", "Vito Coppola", "Lauren Oakley", "Graziano Di Prima", "AJ Pritchard"];
              
              // #23.  Censored: 
              cats["Guardian Cryptic Crossword Setters and Their Pseudonyms Elsewhere"] = ["Adamant", "Arachne", "Rosa Klebb", "Araucaria", "Cinephile", "Armonie", "Chifonie", "Audreus", "Mutt", "Auster", "Bradman", "Giovanni", "Pasquale", "Quixote", "Duck", "Brendan", "Cyclops", "Cincinnus", "Orlando", "Dante", "Rufus", "Doc", "Busman", "Gozo", "Maskarade", "Dogberry", "Shed", "Donk", "Screw", "Enigmatist", "Nimrod", "Io", "Elgar", "Everyman", "Antico", "Columba", "Glow-worm", "Firefly", "Gordius", "Janus", "Logodaedalus", "Mudd", "Dada", "Paul", "Punk"];
              
              // #24
              cats["Musical Acts With UK #1s on 22nd February"] = ["Shirley Bassey", "Anthony Newley", "Elvis Presley", "Frank Ifield", "The Bachelors", "The Kinks", "Nancy Sinatra", "Petula Clark", "Manfred Mann", "Amen Corner", "Edison Lighthouse", "George Harrison", "Chicory Tip", "Suzi Quatro", "Steve Harley & Cockney Rebel", "The Four Seasons", "Leo Sayer", "Lily Allen", "Blondie", "Kenny Rogers", "Joe Dolce Music Theatre", "The Jam", "Kajagoogoo", "Frankie Goes to Hollywood", "Elaine Paige and Barbara Dickson", "Billy Ocean", "Ben E King", "Kylie Minogue", "Simple Minds", "Sinead O'Connor", "Shakespears Sister", "2 Unlimited", "Mariah Carey", "Celine Dion", "Babylon Zoo", "No Doubt", "Cornershop", "Britney Spears", "All Saints", "Atomic Kitten", "Enrique Iglesias", "t.A.T.u", "Busted", "Jennifer Lopez", "Mika"];
              
              // #25.  
              cats["UK Government Departments and Offices"] = ["Attorney General's Office", "Cabinet Office", "Business and Trade", "Culture, Media and Sport", "Education", "Energy Security and Net Zero", "Environment, Food and Rural Affairs", "Science, Innovation and Technology", "Transport", "Work and Pensions", "Health and Social Care", "Export Credits Guarantee", "Foreign, Commonwealth and Development", "Treasury", "Home", "Defence", "Housing, Communities and Local Government", "Justice", "Northern Ireland", "Office of the Advocate General for Scotland", "Office of the Leader of the House of Commons", "Office of the Leader of the House of Lords", "Scotland", "Wales", "Charity Commission", "Competition and Markets Authority", "Crown Prosecution Service", "Food Standards Agency", "Forestry Commission", "Government Actuary's", "Government Legal", "Land Registry", "Revenue & Customs", "NS&I", "National Archives", "National Crime Agency", "Office of Rail and Road", "Ofgem", "Ofqual", "Ofsted", "Serious Fraud", "Supreme Court", "UK Statistics Authority", "Water Services Regulation Authority", "Office of the Prime Minister"];
              
              // #26.  Censored:  
              cats["Words Containing 'Bill'"] = ["Antibillboard", "Bluebill", "Boatbill", "Broadbill", "Cockbill", "Cranesbill", "Crossbill", "Deshabille", "Duckbill", "Gerbillinae", "Handbill", "Hawksbill", "Hedgebill", "Hillbilly", "Hornbill", "Ivorybill", "Misbill", "Morbilli", "Morbilliform", "Morbillivirus", "Multibillion", "Overbill", "Playbill", "Prebill", "Psychobilly", "Razorbill", "Rebill", "Rockabilly", "Saddlebill", "Sawbill", "Sheathbill", "Shoebill", "Sicklebill", "Silverbill", "Spoonbill", "Storksbill", "Swordbill", "Thornbill", "Tourbillon", "Twibill", "Unbilled", "Waxbill", "Waybill", "Windbill", "Wrybill"];
              
              // #27.  Censored: 
              cats["Spiel des Jahres Winners"] = ["Bomb Busters", "Sky Team", "Dorfromantik", "Cascadia", "MicroMacro: Crime City", "Just One", "Azul", "Kingdomino", "Codenames", "Colt Express", "Camel Up", "Hanabi", "Kingdom Builder", "Qwirkle", "Dixit", "Dominion", "Keltis", "Zooloretto", "Thurn and Taxis", "Niagara", "Ticket to Ride", "Alhambra", "Villa Paletti", "Carcassonne", "Torres", "Tikal", "Elfenland", "Mississippi Queen", "El Grande", "The Settlers of Catan", "Manhattan", "Call My Bluff", "Um Reifenbreite", "Drunter und Drüber", "Adel Verpflichtet", "Café International", "Barbarossa", "Auf Achse", "Top Secret Spies", "Sherlock Holmes: Consulting Detective", "Railway Rivals", "Scotland Yard", "Enchanted Forest", "Rummikub", "Hare and Tortoise"];
              
              // #28.  Censored: 
              cats["Top-Scoring Scrabble Words"] = ["Oxyphenbutazone", "Hydroxyzines", "Subjectivizing", "Psychoanalyzing", "Hyperimmunizing", "Subjectivized", "Jackhammering", "Hypnotizability", "Ventriloquizing", "Photooxidizing", "Psychoanalyzed", "Hydroxyzine", "Cyclohexylamine", "Hypercivilized", "Ventriloquized", "Squeezability", "Coenzymatically", "Psychoanalyzes", "Subjectivizes", "Psychobiography", "Hypopharynxes", "Thymectomizing", "Hyperimmunized", "Photooxidized", "Microearthquake", "Demythologizing", "Zygapophyses", "Ventriloquizes", "Hyperpolarizing", "Psychophysicist", "Extrajudicially", "Photosynthesize", "Extemporization", "Hyperimmmunizes", "Psycholigizing", "Psychosexuality", "Phytochemically", "Isocarboxazids", "Squeezabilities", "Schizophrenics", "Overemphasizing", "Hysterectomized", "Exoerythrocytic", "Plethysmography", "Rhythmizations"];
              
              // #29
              cats["Frank Loesser Songs"] = ["Runyonland", "Fugue for Tinhorns", "Follow the Fold", "The Oldest Established", "I'll Know", "A Bushel and a Peck", "Adelaide's Lament", "Guys and Dolls", "Havana", "If I Were A Bell", "My Time of Day", "I've Never Been in Love Before", "Take Back Your Mink", "Adelaide's Second Lament", "More I Cannot Wish You", "The Crapshooter's Dance", "Luck Be A Lady", "Sue Me", "Sit Down, You're Rockin' the Boat", "Marry the Man Today", "Anywhere I Wander", "Baby, It's Cold Outside", "The Ballad of Rodger Young", "Big D", "The Boys in the Back Room", "Heart and Soul", "Hoop-Dee-Doo", "I Don't Want to Walk Without You", "I Hear Music", "I Wish I Didn't Love You So", "Jingle Jangle Jingle", "The Lady's In Love With You", "Let's Get Lost", "Moments Like This", "The Moon of Manakoora", "(I'd Like to Get You on a) Slow Boat to China", "Praise the Lord and Pass the Ammunition", "Somebody Somewhere", "Spring Will Be a Little Late This Year", "Standing on the Corner", "Two Sleepy People", "Wave to Me, My Lady", "What Are You Doing New Year's Eve?", "A Woman in Love", "Wonderful Copenhagen"];
              
              // #30.  Censored:  
              cats["Native British Trees"] = ["Alder", "Alder buckthorn", "Ash", "Aspen", "Common beech", "Downy birch", "Silver birch", "Blackthorn", "Common box", "Purging buckthorn", "Bird cherry", "Wild cherry", "Crab apple", "Dogwood", "Elder", "English elm", "Field elm", "Huntingdon elm", "Wych elm", "Guelder rose", "Hawthorn", "Midland hawthorn", "Hazel", "Holly", "Hornbeam", "Juniper", "Common lime", "Large-leaved lime", "Small-leaved lime", "Field maple", "English oak", "Sessile oak", "Plymouth pear", "Scots pine", "Black poplar", "Rowan", "Spindle", "Whitebeam", "Arran whitebeam", "Rock whitebeam", "Wild service tree", "Bay willow", "Goat willow", "Grey willow", "Yew"];
              
              // #31.  Censored:  
              cats["David Tennant Dr Who Episodes"] = ["The Christmas Invasion", "New Earth", "Tooth and Claw", "School Reunion", "The Girl in the Fireplace", "Rise of the Cybermen", "The Age of Steel", "The Idiot's Lantern", "The Impossible Planet", "The Satan Pit", "Love & Monsters", "Fear Her", "Army of Ghosts", "Doomsday", "The Runaway Bride", "Smith and Jones", "The Shakespeare Code", "Gridlock", "Daleks in Manhattan", "Evolution of the Daleks", "The Lazarus Experiment", "42", "Human Nature", "The Family of Blood", "Blink", "Utopia", "The Sound of Drums", "Last of the Time Lords", "Voyage of the Damned", "Partners in Crime", "The Fires of Pompeii", "Planet of the Ood", "The Sontaran Stratagem", "The Poison Sky", "The Doctor's Daughter", "The Unicorn and the Wasp", "Silence in the Library", "Forest of the Dead", "Midnight", "Turn Left", "The Stolen Earth", "Journey's End", "The Next Doctor", "Planet of the Dead", "The Waters"];
              
              // #32.  Censored: 
              cats["(Cockney) Rhyming Slang"] = ["Adam and Eve", "Apples and pears", "Aunt Joanna", "Barnaby Rudge", "Barnet Fair", "Bob Hope", "Hank Marvin", "Dog and bone", "Bread and honey", "Bricks and mortar", "Brown bread", "Bubble and squeak", "China plate", "Cream crackered", "Dickie bird", "Farmer Giles", "Frog and toad", "Kick and prance", "Loaf of bread", "Lionel Blairs", "Mince pies", "Ones and twos", "Plates of meat", "Pork pie", "Pony and trap", "Raspberry tart", "Rosie Lee", "Round the houses", "Ruby Murray", "Skin and blister", "Tea leaf", "Tit for tat", "Treacle tart", "Trouble and strife", "Whistle and flute", "Boracic lint", "Gregory Peck", "Septic tank", "Syrup of figs", "Turkish bath", "Weasel and stoat", "Scooby-Doo", "Desmond Tutu", "Geoff Hurst", "Attila the Hun"];
              
              // #33.  Censored: 
              cats["Cakes and Pastries of the World"] = ["Pão de Ló de Ovar", "Kladdkaka", "Basque cheesecake", "Kasutera", "Cremeschnitte", "Schwarzwälder Kirschtorte", "Cassata", "Bublanina", "Ciambella", "Lamington", "Tres leches", "Lemon drizzle", "Devil's food", "Galette des rois", "Piernik", "Baumkuchen", "Dobostorta", "Kouign-amann", "Karpatka", "Dacquoise", "Bolo rei", "Prinsesstårta", "Rogel", "Ptichye moloko", "Fraisier", "Potica", "Pineapple upside down", "Carrot", "Coffee and walnut", "Amandine", "Tompouce", "Bibingka", "Wuzetka", "Boterkoek", "Portokalopita", "Financier", "Lapis legit", "Madeira", "Fiadone", "Bizcocho de aceite", "Chiffon", "Battenberg", "Cannelé", "Kransekake", "Hummingbird"];
              
              // #34.  Censored:  
              cats["South American Rivers"] = ["Amazon", "Orinoco", "Paraná", "Tocantins", "Magdalena", "Uruguay", "Essequibo", "Atrato", "São Francisco", "San Juan", "Jacuí", "Maroni", "Courantyne", "Guayas", "Araguari", "Guamá", "Patía", "Oyapock", "Doce", "São Gonçalo", "Paraíba do Sul", "Río Negro", "Catatumbo", "Baudó", "Esmeraldas", "Parnaíba", "Chubut", "Elqui", "Loa", "Maule", "Perquilauquén", "Palena", "Valdivia", "Yelcho", "Marañón", "Tambo", "Putumayo", "Bío-Bío", "Cauca", "Ucayali", "Guaviare", "Rapel", "Petrohué", "Toltén", "Cau-Cau"];
              
              // #35.  Censored:  
              cats["Places in Antarctica"] = ["Drake Passage", "Cuverville Island", "Neko Harbour & Paradise Bay", "Port Lockroy", "Lemaire Channel", "Larsen Ice Shelf", "Snow Hill Island", "Paulet Island", "Bellingshausen Sea", "Ross Ice Shelf", "Petermann Island", "Pendleton Strait", "Amundsen Sea", "Wilhemina Bay", "Wreck of the Guvernøren", "Brown Bluff", "Penguin Post Office", "South Pole", "Scott's Hut", "Vernadsky Research Base", "Chapel of the Snows", "Pendulum Cove", "Mount Erebus", "Vinson Massif", "Deception Island", "Shackleton's Grave", "New Bransfield House", "Base General Bernardo O'Higgins", "Schollaert Channel", "Wordie House", "Inexpressible Island Ice Cave", "Wilson's Stone Igloo", "Primero de Mayo Lighthouse", "Richard Byrd's Bust", "McMurdo Station", "Onyx River", "Lake Vostok", "Dome Argus", "Esperanza Base", "McDonald Beach", "Portal Point", "Schirmacher Oasis", "Ulvetanna Peak", "Blood Falls", "Pole of Inaccessibility"];
              
              // #36.  Censored:  
              cats["Chris de Burgh Songs"] = ["The Lady In Red", "So Beautiful", "Missing You", "Always On My Mind", "Don't Pay The Ferryman", "Here Is Your Paradise", "Sailing Away", "High On Emotion", "Moonlight and Vodka", "Borderline", "Carry Me (Like A Fire In Your Heart)", "Fatal Hesitation", "Where Peaceful Waters Flow", "Tender Hands", "Lonely Sky", "The Traveller", "The Snows of New York", "Spanish Train", "The Getaway", "Say Goodbye To It All", "It's Me (And I'm Ready To Go)", "Ship To Shore", "Patricia The Stripper", "Fire On The Water", "One More Mile To Go", "Separate Tables", "Discovery", "The Girl With April In Her Eyes", "Crusader", "Every Drop Of Rain", "Snow Is Falling", "I Want It (And I Want It Now)", "One Word (Straight To The Heart)", "Taking It To The Top", "Man On The Line", "Transmission Ends", "By My Side", "Making The Perfect Man", "Satin Green Shutters", "The Ballroom of Romance", "A Spaceman Came Travelling", "The Hands of Man", "Where We Will Be Going", "Have A Care", "Natasha Dance"];
              
              // #37.  Censored: 
              cats["Musical Instruments"] = ["Accordion", "Piano", "Balalaika", "Bandoneón", "Banjo", "Clarinet", "Oboe", "Cor anglais", "Bagpipes", "Castanets", "Guiro", "Crwth", "Didgeridoo", "Djembe", "Dulcimer", "Euphonium", "Fiddle", "Cymbals", "Guitar", "Harmonica", "Harp", "Timpani", "Lithophone", "Lute", "Lyre", "Mandolin", "Nose flute", "Ocarina", "Organ", "Pan pipes", "Piccolo", "Recorder", "Saxophone", "Sitar", "Sousaphone", "Tambourine", "Theremin", "Triangle", "Trombone", "Tuba", "Ukulele", "Violin", "Xylophone", "Zither", "Cello"]
              
              // #38
              cats["Constellations"] = ["Andromeda", "Aquarius", "Aquila", "Aries", "Cancer", "Canis Major", "Capricornus", "Cassiopeia", "Centaurus", "Cetus", "Coma Berenices", "Corona Borealis", "Cygnus", "Draco", "Equuleus", "Fornax", "Gemini", "Grus", "Hercules", "Horologium", "Hydra", "Lacerta", "Leo", "Libra", "Lupus", "Lynx", "Monoceros", "Octans", "Orion", "Perseus", "Pegasus", "Pisces", "Sagittarius", "Scorpius", "Serpens", "Taurus", "Triangulum", "Ursa Major", "Ursa Minor", "Virgo", "Vulpecula", "Indus", "Circinus", "Corvus", "Mensa"];
              
              // #39  Censored: 
              cats["Fish"] = ["Gar", "Sole", "Pike", "Anchovy", "Angelfish", "Cod", "Catfish", "Eel", "Carp", "Mackerel", "Bonito", "Herring", "Salmon", "Pickerel", "Lungfish", "Wrasse", "Barracuda", "Sturgeon", "Tetra", "Sea bass", "Tuna", "Blenny", "Blobfish", "Minnow", "Bream", "Lamprey", "Trout", "Goby", "Halibut", "Perch", "Loach", "Clownfish", "Coelacanth", "Elver", "Flounder", "Grouper", "Guppy", "Mudskipper", "Red snapper", "Tilapia", "Turbot", "Monkfish", "Whitebait", "Marlin", "Zebrafish"];
              
              // #40  Censored: 
              cats["Acronyms and Initialisms"] = ["ASAP", "BOGOF", "FOMO", "GOAT", "JPEG", "LASER", "PIN", "RADAR", "SCUBA", "SONAR", "TASER", "YOLO", "GAAP", "PITA", "SNAFU", "AWOL", "FUBAR", "NATO", "RAM", "WYSIWYG", "IKEA", "LEGO", "FTSE", "STEM", "SARS", "ABBA", "BAFTA", "EGOT", "IMAX", "FIFA", "BRB", "DIY", "FYI", "LOL", "GIF", "DINK", "NEET", "SMART", "FLOTUS", "SWAT", "CAPTCHA", "OPEC", "TEFL", "LASIK", "MOMA"];
              
              // #41
              cats["Colours and Pigments"] = ["Ultramarine", "Han purple", "Purple of Cassius", "Cobalt blue", "Cerulean blue", "Scheele's Green", "Verdigris", "Atacamite", "Green earth", "Orpiment", "Cadmium yellow", "Massicot", "Realgar", "Bismuth vanadate orange", "Sanguine", "Red ochre", "Burnt sienna", "Vermilion", "Cinnabar", "Raw umber", "Lamp black", "Mars black", "Perylene Black", "Antimony white", "Lithopone", "Sachtolith", "Coquelicot", "Skobeloff", "Teal", "Viridian", "Ponceau", "Heliotrope", "Byzantium", "Sable", "Xanadu", "Wheaten", "The-devil-in-the-head", "Quercitron", "Annatto", "Mazarine", "Greige", "Gamboge", "Smaragdine", "Puce", "Lovat"];
              
              // #42
              cats["Cryptic Crossword Anagram Indicator Words"] = ["abnormal", "acrobatics", "adjusted", "agitated", "choppy", "confused", "corrupt", "deranged", "dismantled", "disturbed", "eccentric", "erratic", "fragmented", "giddily", "haphazard", "in a mess", "jumbled", "loosely", "medley", "orderly", "out of sorts", "pliably", "prepared", "questionable", "reassembled", "recycled", "rejigged", "shuffled", "salad", "somehow", "strangely", "topsy-turvy", "tortured", "tumbling", "unfamiliar", "unsettled", "unusual", "vacillating", "vigorously", "wacky", "wrongly", "zany", "mashed", "manipulated", "mongrel"];
              
              // #43 (Replacement).  Censored: 
              cats["Useful Spanish Verbs"] = ["abrir", "apagar", "cantar", "cerrar", "comenzar", "correr", "decir", "desear", "despertar", "dormir", "enseñar", "entender", "enviar", "escribir", "esperar", "estudiar", "firmar", "fumar", "estar", "ser", "hablar", "intentar", "jugar", "leer", "llover", "mirar", "nadar", "necesitar", "organizar", "pagar", "pensar", "poner", "precocupar", "querer", "responder", "sentarse", "tener", "tomar", "trabajar", "traducir", "usar", "utilizar", "vender", "ver", "viajar"];
              
              // #44
              cats["Ancient Gods and Goddesses"] = ["Zeus", "Juno", "Poseidon", "Ceres", "Apollo", "Artemis", "Mars", "Minerva", "Hephaestus", "Aphrodite", "Mercury", "Vesta", "Dionysus", "Huitzilopochtli", "Tezcatlipoca", "Quetzalcoatl", "Anubis", "Geb", "Horus", "Imhotep", "Nefertem", "Osiris", "Ra", "Set", "Sobek", "Thoth", "Bastet", "Hathor", "Isis", "Nut", "Sekhmet", "Heimdallr", "Eos", "Selene", "Brahma", "Vishnu", "Shiva", "Krishna", "Loki", "Odin", "Thor", "Freyja", "Frigg", "Ishtar", "Marduk"];
              
              // #45
              cats["FTSE 100 Companies"] = ["3i", "Admiral Group", "Associated British Foods", "Autotrader Group", "BAE Systems", "Barclays", "Berkeley Group Holdings", "British Land", "Bunzl", "Burberry", "Centrica", "Compass Group", "Diageo", "EasyJet", "Endeavour Mining", "Experian", "Fresnillo", "Games Workshop", "Glencore", "Haleon", "Hiscox", "Howdens Joinery", "Weir Group", "IHG Hotels & Resorts", "Imperial Brands", "Informa", "Intertek", "JD Sports", "Kingfisher", "Landsec", "Legal & General", "Lloyds Banking Group", "LondonMetric Property", "M&G", "Marks & Spencer", "Melrose Industries", "Metlen Energy & Metals", "National Grid", "NatWest Group", "Next", "Pershing Square Holdings", "Rentokil Initial", "Rightmove", "Sainsbury's", "Whitbread"];
       
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
