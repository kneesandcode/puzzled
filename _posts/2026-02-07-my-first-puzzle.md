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
              cats["The Archers Characters"] = ["Jill Archer", "Christine Barford", "Leonard Berry", "Clive Horrobin", "Matt Crawford", "Lilian Bellamy", "Shula Hebden Lloyd", "Caroline Bone", "Kenton Archer", "Elizabeth Pargetter", "Helen Titchener", "Brian Aldridge", "Brenda Tucker", "Robin Fairbrother", "Rúairi Donovan", "Clarrie Grundy", "Barbarella", "Emma Carter", "Oliver Sterling", "Siobhán Hathaway", "Adam Macy", "Paddy Redmond", "Simon Gerrard", "Rob Titchener", "Lucas Madikane", "Tracey Horrobin", "Roy Tucker", "Nigel Pargetter", "Sergeant Harrison Burns", "Ian Craig", "Alan Franks", "Usha Gupta", "Richard Locke", "Jack 'Jazzer' McCreary", "Kirsty Miller", "Stella Pryor", "Fallon Rogers", "Lynda Snell", "Zebedee Tring", "Carol Tregorran", "Marjorie Antrobus", "Bert Fry", "Nelson Gabriel", "Sid Perks", "Polly Perkins"];
              
              // #13.  Censored: 
              cats["Buckinghamshire Villages"] = ["Barton Hartshorn", "Bellingdon", "Boarstall", "Bow Brickhill", "Brill", "Cadmore", "Castlethorpe", "Chartridge", "Cholesbury", "Clifton Reynes", "Cublington", "Dagnall", "Drayton Beauchamp", "Edgcott", "Emberton", "Fawley Bottom", "Fingest", "Fulmer", "Gawcott", "Great Horwood", "Grendon Underwood", "Haddenham", "Hambleden", "Hazlemere", "Hoggeston", "Hyde Heath", "Iver", "Jordans", "Kingsey", "Leckhampstead", "Ley Hill", "Marsh Gibbon", "Nether Winchendon", "Newton Blossombille", "Oving", "Padbury", "Quainton", "Ravenstone", "Shabbington", "Speen", "Tylers Green", "Stoke Poges", "Taplow", "Pitchcott", "Mentmore"];
              
              // #14.  Censored:  
              cats["Commonly Misspelled Words, Misspelled"] = ["accomodate", "adress", "adviseable", "agressive", "anually", "becuase", "bouy", "cemetary", "commited", "conceed", "definately", "diffrence", "embarass", "fourty", "independant", "jewelery", "milennium", "miniscule", "neccessary", "occassion", "occured", "possesion", "pronounciation", "questionaire", "recieve", "restaraunt", "rythm", "seperate", "succesful", "suprise", "tommorrow", "untill", "wierd", "withold", "dosen't", "desparate", "dissapoint", "gratefull", "humerous", "inteligence", "momento", "mischevious", "ommision", "privelege", "tomatos"];
              
              // #15
              cats["Most Popular Names for Girls Born in 1950s"] = ["Susan", "Linda", "Christine", "Margaret", "Janet", "Patricia", "Carol", "Elizabeth", "Mary", "Anne", "Ann", "Jane", "Jacqueline", "Barbara", "Sandra", "Gillian", "Pauline", "Elaine", "Lesley", "Angela", "Pamela", "Helen", "Jennifer", "Valerie", "Jean", "Catherine", "Julie", "Kathleen", "Denise", "Maureen", "Judith", "Wendy", "Diane", "Sheila", "Janice", "Lynn", "Karen", "Yvonne", "Shirley", "Marion", "Maria", "Rosemary", "Carole", "Joan", "Marilyn"];
              
              // #16
              cats["Children's TV Programmes 1960s-80s"] = ["Andy Pandy", "The Adventures of Black Beauty", "Bagpuss", "Captain Pugwash", "Catweazle", "Clangers", "Crackerjack!", "Hector's House", "Ivor the Engine", "Jackanory", "The Magic Roundabout", "Magpie", "Mr Benn", "Mr. Men", "Multi-Coloured Swap Shop", "Blue Peter", "Pinky and Perky", "Flower Pot Men", "Rainbow", "Rentaghost", "Roobarb", "Tiswas", "The Woodentops", "What-a-Mess", "The Wombles", "Worzel Gummidge", "The Adventures of Rupert the Bear", "Bleep and Booster", "Camberwick Green", "Captain Scarlet and the Mysterons", "Fireball XL5", "The Herbs", "Joe 90", "Noggin the Nog", "Rag, Tag and Bobtail", "Trumpton", "Muffin the Mule", "The Sooty Show", "Grange Hill", "Animal Kwackers", "DoDo, The Kid from Outer Space", "Pipkins", "Tales of the Riverbank", "The Tomorrow People", "Why Don't You?"];
              
              // #17
              cats["Teas of the World"] = ["Assam", "Ceylon", "Darjeeling", "Golden Monkey", "Sikkim", "Oolong", "Lapsang Souchong", "Lu'an Melon Seed", "Gumpowder", "Matcha", "Sencha", "White Peony", "Silver Needle", "Pu-erh", "Earl Grey", "English Breakfast", "Jasmine", "Masala Chai", "Prince of Wales", "Russian Caravan", "Bohea", "Congou", "Dianhong", "Kangra", "Keemun", "Nilgiri", "Tibeti", "Rize", "Yingdehong", "Dong ding", "Dongfang meiren", "Qilan", "Rougui", "Iron Luohan", "Shui Xian", "Bancha", "Dafang", "Lahpet", "Bu Zhi Chun", "Aracha", "Chun Mee", "Gyokuro", "Hyson", "Mecha", "Junshan Yinzhen"];
              
              // #18.  Censored:  
              cats["Nation's Favourite Poems"] = ["If-", "The Lady of Shalott", "The Listeners", "Not Waving but Drowning", "The Daffodils", "To Autumn", "The Lake Isle of Inisfree", "Dulce et Decorum Est", "Ode to a Nightingale", "He Wishes for the Cloths of Heaven", "Remember", "Elegy Written in a Country Churchyard", "Fern Hill", "Leisure", "The Highwayman", "To His Coy Mistress", "Dover Beach", "The Tyger", "Twelve Songs IX", "Adlestrop", "The Soldier", "Warning", "Sea-Fever", "Upon Westminster Bridge", "Sonnets from the Portuguese XLIII", "The Love Song of J Alfred Prufrock", "Cargoes", "Jabberwocky", "from The Rime of the Ancient Mariner Part I", "Ozymandias of Egypt", "Stopping by Woods on a Snowy Evening", "Abou Ben Adhem", "Everyone Sang", "The Windhover", "Do Not Go Gentle into That Good Night", "Sonnet 18", "When You Are Old", "The Darkling Thrush", "Please Mrs Butler", "Kubla Khan", "Home-Thoughts, from Abroad", "High Flight (An Airman's Ecstasy)", "Journey of the Magi", "The Owl and the Pussy-Cat", "The Road Not Taken"];
              
              // #19.  Censored:  
              cats["Cherry Varieties"] = ["Alba Heart", "Bigarreay Napoleon", "Black Oliver", "Bradbourne Black", "Circassian", "Crooked Billet", "Early Purple Guigne", "Early Rivers", "Empress Eugenie", "Frogmore Early", "Goblin", "Hertfordshire Black", "May Duke", "Merton Glory", "orello", "Nimble Dick", "Noir De Guben", "Nutberry Black", "Prestwood White Heart", "Ruth Tolman", "Smoky Dun", "Strang Loggie", "Yellow Bigarreau", "Yellow Spanish", "Black Tartarian", "Burcombe", "Buttners Spate Rote Knorpelkirsche", "Colney", "Della Recca", "Ebony Pearl", "Flamentiner", "Garden Bing", "Hedelfingen", "Ironsides", "Jubileum", "Kordia", "Montmorency", "Nimba", "Ron's Seedling", "Solymari Gombolyu", "Tragana Edessis", "Yates Seedling", "Starkrimson", "Starking Hardy Giant", "Sutton's Prolific"];
              
              // #20.  Censored:  
              cats["Contestants on The Traitors UK"] = ["Alyssa Chan", "Amanda Lovett", "Wilfred Webster", "Kieran Tompsett", "Ash Bibi", "Harry Clark", "Paul Gorton", "Miles Asteri", "Andrew Jenkins", "Ross Carson", "Armani Gouveia", "Linda Rands", "Minah Shannon", "Charlotte Berman", "Freddie Fraser", "Alan Carr", "Cat Burns", "Jonathan Ross", "Fiona Hughes", "Hugo Lodge", "Rachel Duffy", "Stephen Libby", "Aaron Evans", "Hannah Byczkowski", "Meryl Williams", "Jake Brown", "Leanne Quigley", "Mollie Pearce", "Jaz Singh", "Evie Morrison", "Faraaz Noor", "Francesca Rowden-Plowden", "Alexander Dragonetti", "Jade Scott", "Jack Butler", "James Baker", "Madelyn Smedley", "Jasmine Boatswain", "Roxy Wilson", "Fay Greaves", "Ross Carson", "Matthew Hyndman", "Joe Scott", "Ellie Buckley", "Jessie Stride"];
              
              // #21.  
              cats["Phrases Coined By Shakespeare"] = ["Be cruel to be kind", "Green-eyed monster", "Wear your heart on your sleeve", "The world's your oyster", "Break the ice", "Heart of gold", "What the dickens", "(To be) in stitches", "Apple of my eye", "Foregone conclusion", "Faint-hearted", "High time", "Laughing stock", "More fool you", "Night owl", "Short shrift", "Wild goose chase", "With bated breath", "Brave new world", "Good riddance", "In a pickle", "It's all Greek to me", "Star-crossed lovers", "Dead as a doornail", "Fair play", "Eaten out of house and home", "Hoisted by one's own petard", "In one's heart of hearts", "To kill with kindness", "Lie low", "Melted into thin air", "Salad days", "One's own flesh and blood", "Sound and fury", "Sterner stuff", "The be-all and end-all", "Thereby hangs a tale", "The lady doth protest too much", "A method in one's madness", "Too much of a good thing", "Tower of strength", "A blinking idiot", "All that glitters is not gold", "Jaws of death", "Love is blind"];
              
              // #22  Censored:  
              cats["Strictly Come Dancing Professionals"] = ["Brendan Cole", "Anton Du Beke", "Paul Killick", "Camilla Dallerup", "Erin Boag", "Nicole Cutler", "Ian Waite", "Lilia Kopylova", "Darren Bennett", "Matthew Cutler", "Karen Hardy", "Flavia Cacace", "Ola Jordan", "Vincent Simone", "James Jordan", "Kristina Rihanoff", "Brian Fortuna", "Aliona Vilani", "Katya Virshilas", "Natalie Lowe", "Robin Windsor", "Artem Chigvintsev", "Pasha Kovalev", "Karen Hauer", "Iveta Lukošiūtė", "Kevin Cilfton", "Janette Manrara", "Aljaž Škorjanec", "Joanne Clifton", "Tristan McManus", "Oti Mabuse", "Gleb Savchenko", "Giovanni Pernice", "Gorka Márquez", "Nadiya Bychkova", "Dianne Buswell", "Johannes Radebe", "Nikita Kuzmin", "Nancy Xu", "Kai Widdrington", "Jowita Przystał", "Vito Coppola", "Lauren Oakley", "Graziano Di Prima", "AJ Pritchard"];
              
              // #23.  Censored: 
              cats["Guardian Cryptic Crossword Setters and Their Pseudonyms Elsewhere"] = ["Adamant", "Arachne", "Rosa Klebb", "Araucaria", "Cinephile", "Armonie", "Chifonie", "Audreus", "Mutt", "Auster", "Bradman", "Giovanni", "Pasquale", "Quixote", "Duck", "Brendan", "Cyclops", "Cincinnus", "Orlando", "Dante", "Rufus", "Doc", "Busman", "Gozo", "Maskarade", "Dogberry", "Shed", "Donk", "Screw", "Enigmatist", "Nimrod", "Io", "Elgar", "Everyman", "Antico", "Columba", "Glow-worm", "Firefly", "Gordius", "Janus", "Logodaedalus", "Mudd", "Dada", "Paul", "Punk"];
              
              // #24
              cats["Musical Acts With UK #1s on 22nd February"] = ["Shirley Bassey", "Anthony Newley", "Elvis Presley", "Frank Ifield", "The Bachelors", "The Kinks", "Nancy Sinatra", "Petula Clark", "Manfred Mann", "Amen Corner", "Edison Lighthouse", "George Harrison", "Chicory Tip", "Suzi Quatro", "Steve Harley & Cockney Rebel", "The Four Seasons", "Leo Sayer", "Lily Allen", "Blondie", "Kenny Rogers", "Joe Dolce Music Theatre", "The Jam", "Kajagoogoo", "Frankie Goes to Hollywood", "Elaine Paige and Barbara Dickson", "Billy Ocean", "Ben E King", "Kylie Minogue", "Simple Minds", "Sinead O'Connor", "Shakespears Sister", "2 Unlimited", "Mariah Carey", "Celine Dion", "Babylon Zoo", "No Doubt", "Cornershop", "Britney Spears", "All Saints", "Atomic Kitten", "Enrique Iglesias", "t.A.T.u", "Busted", "Jennifer Lopez", "Mika"];
              
              // #25.  
              cats["UK Government Departments and Offices"] = ["Attorney General's Office", "Cabinet Office", "Business and Trade", "Culture, Media and Sport", "Education", "Energy Security and Net Zero", "Environent, Food and Rural Affairs", "Science, Innovation and Technology", "Transport", "Work and Pensions", "Health and Social Care", "Export Credits Guarantee", "Foreign, Commonwealth and Development", "Treasury", "Home", "Defence", "Housing, Communities and Local Government", "Justice", "Northern Ireland", "Office of the Advocate General for Scotland", "Office of the Leader of the House of Commons", "Office of the Leader of the House of Lords", "Scotland", "Wales", "Charity Commission", "Competition and Markets Authority", "Crown Prosecution Service", "Food Standards Agency", "Forestry Commission", "Government Actuary's", "Government Legal", "Land Registry", "Revenue & Customs", "NS&I", "National Archives", "National Crime Agency", "Office of Rail and Road", "Ofgem", "Ofqual", "Ofsted", "Serious Fraud", "Supreme Court", "UK Statistics Authority", "Water Services Regulation Authority", "Office of the Prime Minister"];
              
              // #26.  Censored:  
              cats["Words Containing 'Bill'"] = ["Antibillboard", "Bluebill", "Boatbill", "Broadbill", "Cockbill", "Cranesbill", "Crossbill", "Deshabille", "Duckbill", "Gerbillinae", "Handbill", "Hawksbill", "Hedgebill", "Hillbilly", "Hornbill", "Ivorybill", "Misbill", "Morbilli", "Morbilliform", "Morbillivirus", "Multibillion", "Overbill", "Playbill", "Prebill", "Psychobilly", "Razorbill", "Rebill", "Rockabilly", "Saddlebill", "Sawbill", "Sheathbill", "Shoebill", "Sicklebill", "Silverbill", "Spoonbill", "Storksbill", "Swordbill", "Thornbill", "Tourbillon", "Twibill", "Unbilled", "Waxbill", "Waybill", "Windbill", "Wrybill"];
              
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
