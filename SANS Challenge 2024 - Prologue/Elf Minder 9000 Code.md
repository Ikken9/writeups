# Elf Minder 9000 Code

## Game Logic
```javascript
HTTP/2 200 OK
Content-Type: text/javascript;charset=utf-8
Content-Length: 47167
Date: Thu, 05 Dec 2024 23:04:05 GMT
Via: 1.1 google
Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000

const canvas = document.getElementById('gamecanvas');
const ctx = canvas.getContext('2d');

const adminControls = document.querySelector('.admin-controls');

const startBtn = document.getElementById('startBtn');
const resetBtn = document.getElementById('resetBtn');
const trashBtn = document.getElementById('trashBtn');
const helpBtn = document.getElementById('helpBtn');
const toolSelect = document.getElementById('tool-select');
const clearPathBtn = document.getElementById('clearPathBtn');
const clearEntitiesBtn = document.getElementById('clearEntitiesBtn');

const pathToolBtn = document.getElementById('pathToolBtn');
const eraserToolBtn = document.getElementById('eraserToolBtn');
const portalToolBtn = document.getElementById('portalToolBtn');
const springToolBtn = document.getElementById('springToolBtn');

const winDialog = document.querySelector('.dialog.youwin');

const scoreboardParent = winDialog.querySelector('.scoreboard');
const winStats = winDialog.querySelector('.stats');

const mainMenuBtn = winDialog.querySelector('button.mainMenuBtn');
const replayBtn = winDialog.querySelector('button.replayBtn');
const nextLevelBtn = winDialog.querySelector('button.nextLevelBtn');

const errorDialog = document.querySelector('.dialog.error');
const replayBtn2 = errorDialog.querySelector('button.replayBtn');
const mainMenuBtn2 = errorDialog.querySelector('button.mainMenuBtn');

const helpDialog = document.querySelector('.dialog.help');
const helpContent = helpDialog.querySelector('div.help-content');
const helpTopics = helpDialog.querySelectorAll('ul.toc li');
const helpVideo = helpDialog.querySelector('video');
const helpCloseBtn = helpDialog.querySelector('button.closeBtn');

const usernameLabel = document.querySelector('span.username');
const goHomeButton = document.querySelector('button.go-home');

const milestoneDialog = document.querySelector('.dialog.milestone');
const milestoneText = milestoneDialog.querySelector('p');
const milestoneBtn = milestoneDialog.querySelector('button.milestoneBtn');
const milestoneCloseBtn = milestoneDialog.querySelector('button.closeBtn');
const closeBtn = milestoneDialog.querySelector('button.closeBtn');

const appTitle = document.querySelector('.app-title');

/* <li>Basic Gameplay</li>
<li>Scoring</li>
<li>Sizzling Sand</li>
<li>Sleepy Crab</li>
<li>Portals</li> */

const HelpContent = {
    'Basic Gameplay': {
        content: `
        <video width="356" height="348" autoplay muted loop>
            <source src="./instructions.mp4" type="video/mp4">
        </video>
        <div class="instructions">
            <strong>Instructions</strong>
            <ol>
                <li>Draw a path for the elf.</li>
                <li>Collect all crates.</li>
                <li>Head to the checkered flag.</li>
            </ol>
        </div>
        `
    },
    'Path Rotation': {
        content: `
        <video width="356" height="348" autoplay muted loop>
            <source src="./rotate.mp4" type="video/mp4">
        </video>
        <div class="instructions">
            <strong>Path Rotation</strong>
            <p>Click on a path segment to rotate it. You cannot rotate paths on an occupied cell.</p>
        </div>
        `
    },
    'Scoring': {
        content: `
        <div class="instructions">
            <h3>Scoring</h3>
            <p>Scoring happens in two modes: <strong>Good Boss</strong>, and <strong>Evil Boss</strong>.</p>
            <div>
                <p>A <strong>Good Boss</strong> tries to get work done efficiently, so <strong>Good Boss</strong> scoring is calculated using the formula:</p>
                <img src="./goodscore.png" alt="Good Boss Score Formula" />
            </div>
            <div>
                <p>An <strong>Evil Boss</strong> wastes your time and doesn't pitch in, so <strong>Evil Boss</strong> is scored using this formula:</p>
                <img src="./evilscore.png" alt="Evil Boss Score Formula" />
            </div>
        </div>
        `
    },
    'Tunnels': {
        content: `
        <video width="356" height="348" autoplay muted loop>
            <source src="./portal.mp4" type="video/mp4">
        </video>
        <div class="instructions">
            <strong>Tunnels</strong>
            <ol>
                <li>Tunnels teleport the elf.</li>
                <li>Tunnels come in pairs.</li>
                <li>You can only dig one tunnel.</li>
            </ol>
        </div>
        `
    },
    'Springs': {
        content: `
        <video width="356" height="348" autoplay muted loop>
            <source src="./spring.mp4" type="video/mp4">
        </video>
        <div class="instructions">
            <strong>Springs</strong>
            <p>When stepped on, springs launch the elf in the current direction of travel. If there isn't a path in that direction, the elf will skip the spring.</p>
        </div>
        `
    },
    'Sizzling Sand': {
        content: `
        <video width="356" height="348" autoplay muted loop>
            <source src="./steam.mp4" type="video/mp4">
        </video>
        <div class="instructions">
            <strong>Sizzling Sand</strong>
            <p>You walk quickly on sizzling sand.</p>
        </div>
        `
    },
    'Sleepy Crab': {
        content: `
        <video width="356" height="348" autoplay muted loop>
            <source src="./crab.mp4" type="video/mp4">
        </video>
        <div class="instructions">
            <strong>Sleepy Crab</strong>
            <p>You walk slowly near sleeping crabs.</p>
        </div>
        `
    },
};

const showHelpContent = section => {
    helpContent.innerHTML = HelpContent[section].content;
    helpDialog.classList.remove('hidden');
    helpTopics.forEach(t => t.classList.remove('active'));
    helpDialog.querySelector(`li[topic="${section}"]`).classList.add('active');
};

helpBtn.addEventListener('click', () => {
    showHelpContent('Basic Gameplay');
});

const renderToolSelection = () => {
    [
        pathToolBtn,
        eraserToolBtn,
        portalToolBtn,
        springToolBtn,
    ].forEach(btn => {
        btn.classList.remove('active');
    });

    const selectedToolBtn = document.getElementById(`${selectedTool}ToolBtn`);

    if (selectedToolBtn) {
        selectedToolBtn.classList.add('active');
    }
};

const selectTool = event => {
    const tool = event.target.getAttribute('id');
    selectedTool = tool.split('Tool')[0];
}

pathToolBtn.addEventListener('click', selectTool);
eraserToolBtn.addEventListener('click', selectTool);
portalToolBtn.addEventListener('click', selectTool);
springToolBtn.addEventListener('click', selectTool);

const hideHelpContent = () => {
    helpDialog.classList.add('hidden');
};

helpCloseBtn.addEventListener('click', hideHelpContent);

helpTopics.forEach(topic => {
    topic.setAttribute('topic', topic.innerText);
    topic.addEventListener('click', () => {
        const section = topic.innerText;
        showHelpContent(section);

        // remove active class from all topics
    });
});

const setWinDialogText = str => {
    winDialog.querySelector('pre').innerText = str;
}

// example scoreboard data
// [
//     {
//       "id": 2,
//       "username": "::1",
//       "level": 0,
//       "score": 658,
//       "ticks": 252,
//       "segments": 18,
//       "clicks": 0,
//       "data": "{\"segments\":18,\"clicks\":0,\"level\":0,\"ticks\":252,\"goodScore\":658,\"evilScore\":306}",
//       "timestamp": 1724432003144
//     },
//     {
//       "id": 3,
//       "username": "::1",
//       "level": 0,
//       "score": 0,
//       "ticks": 812,
//       "segments": 58,
//       "clicks": 0,
//       "data": "{\"segments\":58,\"clicks\":0,\"level\":0,\"ticks\":812,\"goodScore\":0,\"evilScore\":986}",
//       "timestamp": 1724432097954
//     }
//   ]

const dressUpScoreboardField = (field, data) => {
    switch(field) {
        case 'level':
            return Levels[data].name;
        // case 'ticks':
        //     return data || 0;
        // case 'segments':
        //     return data || 0;
        // case 'clicks':
        //     return data[field] || 0;
        case 'timestamp':
            return new Date(data).toLocaleString();
        default:
            return data;
    }
};

const generateHTMLTableScoreboard = (data, mode) => {
    const table = document.createElement('table');
    table.classList.add(mode);

    if (mode === 'evil') table.classList.add('hidden');

    const thead = document.createElement('thead');
    const tbody = document.createElement('tbody');
    const headerRow = document.createElement('tr');

    const headers = Object.keys(data[0]).filter(header => !~['id', 'timestamp'].indexOf(header));

    // Add position header
    const positionHeader = document.createElement('th');
    positionHeader.innerText = '';
    headerRow.appendChild(positionHeader);

    headers.forEach(header => {
        const th = document.createElement('th');
        th.innerText = header;
        headerRow.appendChild(th);
    });

    thead.appendChild(headerRow);
    table.appendChild(thead);

    for (let i = 0; i < 10; i++) {
        const rowData = data[i];
        if (rowData) {
            const tr = document.createElement('tr');
            // Add position column
            const positionColumn = document.createElement('td');
            positionColumn.classList.add('position-text')
            positionColumn.innerText = i + 1;
            tr.appendChild(positionColumn);

            headers.forEach(header => {
                const td = document.createElement('td');
                td.innerText = dressUpScoreboardField(header, rowData[header]);
                tr.appendChild(td);
            });
            tbody.appendChild(tr);
        } else {
            const tr = document.createElement('tr');
            // Add position column
            const positionColumn = document.createElement('td');
            positionColumn.classList.add('position-text')
            positionColumn.innerText = i + 1;
            tr.appendChild(positionColumn);

            headers.forEach(header => {
                const td = document.createElement('td');
                td.innerText = '';
                tr.appendChild(td);
            });
            tbody.appendChild(tr);
        }
    }

    table.appendChild(tbody);

    return table;
}

const showStats = data => {
    // clear stats element first
    winStats.innerHTML = '';
    
    [ 
        'good',
        'evil',
    ].forEach(mode => {
        // create parent div
        const parentDiv = document.createElement('div');
        parentDiv.classList.add('stats-parent');
        parentDiv.classList.add(mode);

        if (mode === 'evil') parentDiv.classList.add('hidden');
    
        // create h3 tag for score
        const h3 = document.createElement('h2');
    
        h3.innerText = `Score: ${data.data[`${mode}Score`]}`;
        parentDiv.appendChild(h3);

        const detailsPre = document.createElement('pre');
        detailsPre.innerText = `Ticks: ${data.data.ticks} \t Segments: ${data.data.segments} \t Clicks: ${data.data.clicks}`;

        parentDiv.appendChild(detailsPre);
        winStats.appendChild(parentDiv);
    });
};

[ ...document.querySelectorAll('.dialog.youwin .mode-switch button') ].forEach(button => {
    button.addEventListener('click', () => {
        const mode = button.getAttribute('data-mode');
        
        document.querySelectorAll('.dialog.youwin .mode-switch button')[0].classList.remove('active');
        document.querySelectorAll('.dialog.youwin .mode-switch button')[1].classList.remove('active');

        document.querySelector(`.dialog.youwin .mode-switch button[data-mode='${mode}']`).classList.add('active');
        
        document.querySelector('.dialog.youwin .stats-parent.good').classList.add('hidden');
        document.querySelector('.dialog.youwin .stats-parent.evil').classList.add('hidden');

        document.querySelector(`.dialog.youwin .stats-parent.${mode}`).classList.remove('hidden');

        document.querySelector('.dialog.youwin table.good').classList.add('hidden');
        document.querySelector('.dialog.youwin table.evil').classList.add('hidden');

        document.querySelector(`.dialog.youwin table.${mode}`).classList.remove('hidden');
    });
});

const showWinDialog = data => {
    // setWinDialogText(str);

    showStats(data);

    if (!nextLevel) {
        nextLevelBtn.setAttribute('disabled', 'disabled');
    }

    document.querySelectorAll('.dialog.youwin .mode-switch button')[1].classList.remove('active');
    document.querySelectorAll('.dialog.youwin .mode-switch button')[0].classList.add('active');

    scoreboardParent.appendChild(generateHTMLTableScoreboard(data.goodScores, 'good'));
    scoreboardParent.appendChild(generateHTMLTableScoreboard(data.evilScores, 'evil'));

    winDialog.classList.remove('hidden');
}

const setErrorDialogText = str => {
    errorDialog.querySelector('h2').innerText = str;
}

const showErrorDialog = str => {
    setErrorDialogText(str);
    errorDialog.classList.remove('hidden');
}

replayBtn.addEventListener('click', () => {
    window.location.reload();
});

replayBtn2.addEventListener('click', () => {
    window.location.reload();
});

mainMenuBtn.addEventListener('click', () => {
    window.location.href = `index.html?id=${rid}`;
});

mainMenuBtn2.addEventListener('click', () => {
    window.location.href = `index.html?id=${rid}`;
});

closeBtn.addEventListener('click', () => {
    milestoneDialog.classList.add('hidden');
});

goHomeButton.addEventListener('click', () => {
    window.location.href = `index.html?id=${rid}`;
});


nextLevelBtn.addEventListener('click', () => {
    if (nextLevel) {
        window.location.href = `?id=${rid}&level=${nextLevel}`;
    }
});

const levelId = new Chance().integer({ min: 100000000, max: 999999999 });
const chance = new Chance(levelId);
let chanceStable;

const timeLimit = 999;
const rows = 10;
const cols = 12;
// const cellSize = canvas.width / cols;
const bgTileSize = cellSize * 2;

const SNAP_DISTANCE = 10;

let controlPoint;
// let segments = [];
let clicks = [];
let isMouseDown = false;
let cursor = [1, 1];
let dashOffset = 0;
let hero = {};

let entities;

let mode = -1; // -1: preflight, 0: edit, 1: run, 2: scoreboard

const backgroundColors = [
    '#D2B48C', // 0: sand 1
    '#DEB887', // 1: sand 2
    'blue', // 2: water
    'black', // 3: hazard
];

const ImageAssets = {
    crate: 'crate.png',
    elf: 'elf.png',
    elf_nervous: 'elf_nervous.png',
    elf_hot: 'elf_hot.png',
    x: 'x.png',
    crab: 'crab.png',
    crab2: 'crab2.png',
    start: 'start.png',
    blocker: 'blocker.png',
    startflag: 'startflag.png',
    finishflag: 'finishflag.png',
    steam1: 'steam1.png',
    steam2: 'steam2.png',
    steam3: 'steam3.png',
    portal: 'portal.png',
    spring: 'spring.png',
};

let toLoad = Object.keys(ImageAssets).length;

Object.keys(ImageAssets).forEach(imageId => {
    const img = new Image();
    img.src = ImageAssets[imageId];
    ImageAssets[imageId] = img;
    img.onload = () => {
        toLoad--;
        if (toLoad === 0) {

        }
    };
});

const drawImageWithTransform = (img, x, y, rotation=0, xScale=1, yScale=1, origin={x: 0, y: 0}, opacity=1) => {
    ctx.save();  
    ctx.globalAlpha = opacity;
    ctx.translate(x, y);
    ctx.rotate(rotation);
    ctx.scale(xScale, yScale); 
    ctx.drawImage(
      img, 
      -origin.x, 
      -origin.y, 
      img.width, 
      img.height
    );
    ctx.restore(); 
}

let selectedTool = 'path';

const getSelectedTool = () => !isEditor ? selectedTool : [ ...document.querySelectorAll('[name="tool-select"]')].find(d => d.checked).value;

const __PARSE_URL_VARS__ = () => {
    let vars = {};
    window.location.href.replace(/[?&]+([^=&]+)=([^&]*)/gi, function(m, key, value) {
        vars[key] = value;
    });
    return vars;
}

const urlDecode = str => {
    return decodeURIComponent((str + '').replace(/\+/g, '%20'));
}

const urlParams = __PARSE_URL_VARS__();
const levelNum = urlParams.level ? urlDecode(urlParams.level) : '';
const rid = urlParams.id;
const isEditor = !!urlParams.edit;

if (isEditor) {
    adminControls.classList.remove('hidden');
    console.log('⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡');
    console.log('⚡ Hey, I noticed you are in edit mode! Awesome!');
    console.log('⚡ Use the tools to create your own level.');
    console.log('⚡ Level data is saved to a variable called `game.entities`.');
    console.log('⚡ I\'d love to check out your level--');
    console.log('⚡ Email `JSON.stringify(game.entities)` to evan@counterhack.com');
    console.log('⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡⚡');
}

let levelEntities;
let game;

// showHelpContent('Basic Gameplay');

const EntityHelpMap = {
    0: 'Basic Gameplay',
    1: 'Path Rotation',
    4: 'Sleepy Crab',
    5: 'Sizzling Sand',
    6: 'Tunnels',
    7: 'Springs',
};

const saveSegmentsAndEntitiesToLocalStorage = () => {
    try {
        if (typeof levelNum === 'undefined') return;

        game.dedupeSegments();
        game.disappointHackers();
        
        localStorage.setItem(`level_${levelNum}`, JSON.stringify({
            segments: game.segments,
            entities: game.entities.filter(entity => ~[EntityTypes.SPRING, EntityTypes.PORTAL].indexOf(entity[2])),
        }));

        if (game.hasStartSegments()) {
            startBtn.classList.add('ready');
        }  else {
            startBtn.classList.remove('ready');
        }

    } catch (e) {
        console.error('error saving level:', e);
    }
}

const refreshSegmentsAndEntitiesFromLocalStorage = () => {
    if (typeof levelNum === 'undefined') return;
    try {
        const levelData = JSON.parse(localStorage.getItem(`level_${levelNum}`));
        if (levelData) {
            game.segments = levelData.segments;
            game.entities = game.entities.concat(levelData.entities);

            if (game.hasStartSegments()) {
                startBtn.classList.add('ready');
            }  else {
                startBtn.classList.remove('ready');
            }
        }
    } catch (e) {
        console.error('error refreshing level:', e);
        game.segments = [];
    }
}

const hideLogoScreen = () => {
    document.querySelector('.dialog.logo').classList.add('hidden');
}

const generateListOfLevels = levelNames => {
    levelNames.forEach((name, i) => {
        const li = document.createElement('div');
        li.classList.add('level-item');
        if (completedLevels.includes(name)) {
            li.classList.add('completed');
        }
        const a = document.createElement('a');
        a.innerText = name;
        a.href = `index.html?id=${rid}&level=${encodeURIComponent(name)}`;
        li.appendChild(a);
        document.querySelector('.dialog.logo .level-list').appendChild(li);
        if ((completedLevels || []).length >= 12) {
            document.querySelector('.dialog.logo .level-list').classList.add('completed');
        }
    });
    // return ul;
}

let nextLevel;
let completedLevels = [];
let originalEntities;
let level;

const setGameEnv = async () => {
    const env = await fetch(`/game/new?rid=${rid}&level=${levelNum}`).then(async res => await res.json());
    
    if (env.error) {
        console.log('error:', env.error);
    } else {
        usernameLabel.innerText = `User: ${env.username}`;
        window.title = `${env.title} -- Username: ${env.username}`;
        if (env.levelData) {

            level = env.levelData;
            
            nextLevel = env.nextLevel;

            hideLogoScreen();

            appTitle.innerText = `ELF MINDER 9000 v1.2  [${levelNum}]`;
            
            levelEntities = [ ...level.entities ];
            originalEntities = [ ...level.entities ];

            const helpToDisplay = Object.keys(EntityHelpMap).find(key => levelEntities.some(entity => entity[2].toString() === key) && !!!localStorage.getItem(`showHelp:${key}`));
            if (typeof helpToDisplay !== 'undefined') {
                localStorage.setItem(`showHelp:${helpToDisplay}`, 'true');  
                showHelpContent(EntityHelpMap[helpToDisplay]);
            }

            game = new Game({
                entities: levelEntities,
                segments: [],
                clicks: [],
                level: levelNum,
            }, Levels)

            game.entities = [ ...game.entities.filter(d => d[2] === EntityTypes.PORTAL), ...levelEntities ];

            mode = 0;
        
            refreshSegmentsAndEntitiesFromLocalStorage();

            march();
            drawLoop();
        } else {
            completedLevels = env.completedLevels;
            const levelList = generateListOfLevels(env.levelNames);
        }
    }

};

// !Levels[levelNum]

if (!rid) {
    alert('Invalid Resource ID');
} else {
    // fetch /new 
    setGameEnv();
}

function getRandomSandColor() {
    return backgroundColors[chanceStable.bool() ? 0 : 1];
}


const drawBackground = () => {
    chanceStable = new Chance(levelId);
    ctx.strokeStyle = 'rgba(209,199,187,.4)'; // Grid color
    for (let i = 0; i < cols / 2; i++) {
        for (let j = 0; j < rows / 2; j++) {
            if (i >= cols - 1) {
                ctx.fillStyle = 'blue';
            } else {
                ctx.fillStyle = getRandomSandColor();
            }
            ctx.fillRect(i * bgTileSize, j * bgTileSize, bgTileSize, bgTileSize);
            ctx.strokeRect(i * bgTileSize, j * bgTileSize, bgTileSize, bgTileSize);
        }
    }
}

const drawDotOfGivenSize = (x, y, size) => {
    ctx.beginPath();
    ctx.arc(x, y, size, 0, 2 * Math.PI);
    ctx.fill();
}

const doesSegmentExist = ([start, end]) => game.segments.some(segment => {
    if (!start || !end) return false;
    return (segment[0][0] === start[0] && segment[0][1] === start[1] && segment[1][0] === end[0] && segment[1][1] === end[1]) ||
           (segment[0][0] === end[0] && segment[0][1] === end[1] && segment[1][0] === start[0] && segment[1][1] === start[1]);
});


// draw a dot at each cell intersection
const drawGridPoints = () => {
    const tool = getSelectedTool();
    for (let i = 1; i < cols; i+= 1) {
        for (let j = 1; j < rows; j+= 1) {
            ctx.fillStyle = 'rgba(0,0,0,.25)';
            if (!(i % 2 === 0 && j % 2 === 0)) {
                drawDotOfGivenSize(i * cellSize, j * cellSize, 2);
            }
            if (isMouseDown &&
                mode === 0 &&
                tool === 'path' &&
                cursor &&
                (Math.abs(cursor[0] - i) + Math.abs(cursor[1] - j)) === 1) {
                    const midpoint = game.getSegmentMidpoint([cursor, [i, j]]);
                    const cellMidpoint = game.getCellByPoint(midpoint.map(d => d * cellSize));
                    const oneWay = game.isCellOneWay(cellMidpoint);
                    
                    if (!doesSegmentExist([[i, j], controlPoint]) &&
                    !(oneWay && isPointInAnySegment(cellMidpoint)) &&
                    !isPointInTwoSegments([i, j]) &&
                    !isPointInTwoSegments(cursor) &&
                    !isPointBlocked([i, j]) &&
                    !(i % 2 === 0 && j % 2 === 0)) {

                        ctx.fillStyle = 'rgba(0,255,0,.5)';
                        ctx.save();
                        ctx.strokeStyle = 'rgba(0,255,0,.5)';
                        ctx.beginPath();
                        ctx.lineWidth = 4;
                        ctx.setLineDash([6, 4]);
                        ctx.lineDashOffset = -dashOffset;
                        ctx.moveTo(cursor[0] * cellSize, cursor[1] * cellSize);
                        ctx.lineTo(i * cellSize, j * cellSize);
                        ctx.stroke();
                        ctx.restore();
                        drawDotOfGivenSize(i * cellSize, j * cellSize, 6);
                    }
            }
        }
    }
    if (isMouseDown && cursor && tool === 'path') {
        ctx.save();
        ctx.strokeStyle = 'rgba(255,255,0,.5)';
        ctx.beginPath();
        ctx.lineWidth = 4;
        ctx.setLineDash([6, 4]);
        ctx.lineDashOffset = -dashOffset;
        ctx.moveTo(cursor[0] * cellSize, cursor[1] * cellSize);
        ctx.lineTo(mouseX, mouseY);
        ctx.stroke();
        ctx.restore();
    }
}

let highlightCell;


let mouseX, mouseY;

const isPointInSegment = (point, segment) => {
    const [start, end] = segment;
    const [x, y] = point;
    return start[0] === x && start[1] === y || end[0] === x && end[1] === y;
}

const isPointInAnySegment = point => {
    return game.segments.some(segment => {
        return isPointInSegment(point, segment);
    });
}

const deleteSegmentsWithPoint = point => {
    game.segments = game.segments.filter(segment => {
        return !(point[0] === segment[0][0] && point[1] === segment[0][1]);
    });
}

const isPointInTwoSegments = point => {
    let count = 0;
    game.segments.forEach(segment => {
        if (isPointInSegment(point, segment)) {
            count++;
        }
    });
    return count >= 2;
}

const getEntitiesByType = type => game.entities.filter(entity => entity[2] === type);

const getEntitiesAtCell = ([x, y]) => game.entities.filter(entity => entity[0] === x && entity[1] === y);

const isPointBlocked = point => {
    const blockers = game.entities.filter(entity => ~[EntityTypes.BLOCKER, EntityTypes.HAZARD].indexOf(entity[2]));  
    return blockers.some(blocker => {
        return blocker[0] === point[0] && blocker[1] === point[1];
    });
}

const isPointAvailable = ([pointX, pointY]) => {
    const midpoint = getSegmentMidpoint([cursor, [pointX, pointY]]);
    const controlPointCell = getCellByPoint(midpoint.map(d => d * cellSize));
    const oneWay = game.isCellOneWay(controlPointCell);
    return cursor &&
        controlPoint &&
        !isPointBlocked([pointX, pointY]) &&
        !(oneWay && isPointInAnySegment(controlPointCell)) &&
        !doesSegmentExist([[pointX, pointY], cursor]) &&
        (Math.abs(cursor[0] - pointX)) + (Math.abs(cursor[1] - pointY)) === 1 && 
        !isPointInTwoSegments(controlPoint) &&
        !isPointInTwoSegments(cursor);
};

const getCellByPoint = ([x, y]) => {
    const subcell = [ Math.floor(x / cellSize), Math.floor(y / cellSize) ];
    return [ subcell[0] % 2 === 0 ? subcell[0] + 1 : subcell[0], subcell[1] % 2 === 0 ? subcell[1] + 1 : subcell[1] ];
};

const getSegmentMidpoint = ([start, end]) => {
    return [ (start[0] + end[0]) / 2, (start[1] + end[1]) / 2 ];
};

const erasableElements = isEditor ? 
    Object.values(EntityTypes) :
    [ EntityTypes.SPRING, EntityTypes.PORTAL ];

canvas.addEventListener('mousemove', (event) => {
    const rect = canvas.getBoundingClientRect();
    mouseX = event.clientX - rect.left;
    mouseY = event.clientY - rect.top;
    
    highlightCell = getCellByPoint([mouseX, mouseY]);

    let pointFound = false;

    const pointX = Math.round(mouseX / cellSize);
    const pointY = Math.round(mouseY / cellSize);
    
    const tool = getSelectedTool();
    
    if (mode === 0) {
        if (!(pointX % 2 === 0 && pointY % 2 === 0) &&
            pointX !== 0 &&
            pointY !== 0 &&
            pointX < cols &&
            pointY < rows) {
            pointFound = true;
            controlPoint = [pointX, pointY];
            if (isMouseDown && 
                tool === 'path' &&
                isPointAvailable([pointX, pointY])) {
                game.segments.push([cursor, [pointX, pointY]]);
                cursor = [pointX, pointY];
                
                saveSegmentsAndEntitiesToLocalStorage();

            } else if (isMouseDown && tool === 'eraser') {
                if (!(pointX % 2 === 0 && pointY % 2 === 0) &&
                    pointX !== 0 &&
                    pointY !== 0 &&
                    pointX < cols &&
                    pointY < rows) {
                    
                    // if (isMouseDown) {
                    deleteSegmentsWithPoint([pointX, pointY]);
                    // }
                    const entitiesAtCell = getEntitiesAtCell([pointX, pointY])
                        .filter(entity => ~erasableElements.indexOf(entity[2]));
                    entitiesAtCell.forEach(entity => {
                        game.entities = game.entities.filter(e => e.join(',') !== entity.join(','));
                    });


                    saveSegmentsAndEntitiesToLocalStorage();
                }
            }
        }
    } else if (mode === 1) {
        pointFound = true;
        controlPoint = [pointX, pointY];
        const cellX = Math.floor(mouseX / cellSize);
        const cellY = Math.floor(mouseY / cellSize);
        const point = [ cellX % 2 === 0 ? cellX + 1 : cellX, cellY % 2 === 0 ? cellY + 1 : cellY ];
        highlightCell = point;
    }

    if (!pointFound && controlPoint) {
        controlPoint = null;
    } 
});

const getCellMidpoint = ([x, y]) => {
    return [ x % 2 === 0 ? x + 1 : x, y % 2 === 0 ? y + 1 : y ]
};

canvas.addEventListener('mousedown', () => {
    if (mode === 0 && controlPoint) {
        isMouseDown = true;
        cursor = controlPoint;
        const tool = getSelectedTool();
        const cellMidpoint = getCellMidpoint(controlPoint);
        
        if (tool === 'path' && !isPointInTwoSegments(controlPoint)) {
            
        } else if (tool === 'start') {
            if (!game.entities.some(entity => entity[2] === EntityTypes.START)) game.entities.push([...cellMidpoint, EntityTypes.START]);
        } else if (tool === 'end') {
            if (!game.entities.some(entity => entity[2] === EntityTypes.END)) game.entities.push([...cellMidpoint, EntityTypes.END]);
        } else if (tool === 'crate') {
            const existingEntities = getEntitiesAtCell(cellMidpoint);
            if (!existingEntities.length) game.entities.push([...cellMidpoint, EntityTypes.CRATE]);
        } else if (tool === 'blocker') {
            const existingEntities = getEntitiesAtCell(cellMidpoint);
            if (!existingEntities.length) game.entities.push([...cellMidpoint, EntityTypes.BLOCKER]);
        } else if (tool === 'hazard') {
            const existingEntities = getEntitiesAtCell(cellMidpoint);
            if (!existingEntities.length) game.entities.push([...cellMidpoint, EntityTypes.HAZARD]);
        } else if (tool === 'steam') {
            const existingEntities = getEntitiesAtCell(cellMidpoint);
            if (!existingEntities.length) game.entities.push([...cellMidpoint, EntityTypes.STEAM]);
        } else if (tool === 'spring') {
            const existingEntities = getEntitiesAtCell(cellMidpoint);
            const relevantEntities = existingEntities.filter(([,,type]) => ~[
                EntityTypes.SPRING,
                EntityTypes.START,
                EntityTypes.END,
                EntityTypes.CRATE,
                EntityTypes.BLOCKER,
                EntityTypes.HAZARD,
            ].indexOf(type));
            const springsAtPoint = getEntitiesAtCell(cursor).filter(([,,type]) => type === EntityTypes.SPRING);
            if (!relevantEntities.length && !springsAtPoint.length) {
                const existingSprings = getEntitiesByType(EntityTypes.SPRING);
                if (existingSprings.length === 2) {
                    // remove just the oldest spring
                    const oldestSpring = game.entities.findIndex(entity => entity[2] === EntityTypes.SPRING);
                    game.entities.splice(oldestSpring, 1);
                } 
                // game.entities = game.entities.filter(entity => entity[2] !== EntityTypes.SPRING);
                game.entities.push([...cursor, EntityTypes.SPRING]);
            }
        } else if (tool === 'portal') {
            const existingEntities = getEntitiesAtCell(cellMidpoint);
            if (!existingEntities.length) {

                const inTwoSegments = isPointInTwoSegments(cellMidpoint);
                if (inTwoSegments) return;
                
                const existingPortals = getEntitiesByType(EntityTypes.PORTAL);
                if (existingPortals.length === 2) {
                    // remove just the oldest portal
                    const oldestPortal = game.entities.findIndex(entity => entity[2] === EntityTypes.PORTAL);
                    game.entities.splice(oldestPortal, 1);
                }
                game.entities.push([...cellMidpoint, EntityTypes.PORTAL]);
            }
        }
        
        saveSegmentsAndEntitiesToLocalStorage();

    } else if (mode === 1) {
        const rect = canvas.getBoundingClientRect();
        const x = event.clientX - rect.left;
        const y = event.clientY - rect.top;
        const midpoint = getCellByPoint([ x, y ]);
        if (!isPointInSegment(midpoint, game.hero.journey)) game.addClick(midpoint); // game.rotateSegments(midpoint, true);
    }
});

canvas.addEventListener('mouseup', () => {
    isMouseDown = false;
});

// add event listener to track state of the spacebar
let spaceDown = false;
document.addEventListener('keydown', event => {
    if (event.code === 'Space') {
        spaceDown = true;
    }
});

document.addEventListener('keyup', event => {
    if (event.code === 'Space') {
        spaceDown = false;
    }
});

const modeText = [
    'Draw',
    'Run',
];

let gameLoopInterval;

const stopGame = () => {
    mode = 2;
    startBtn.classList.remove('active');
    startBtn.innerText = 'Start';
    clearInterval(gameLoopInterval);
    walkingSfx.pause();
    crabSfx.pause();
    sizzleSfx.pause();
};

const sizzleSfx = new Audio('sizzle.mp3');
sizzleSfx.loop = true;
sizzleSfx.volume = 0;

const crabSfx = new Audio('crab.mp3');
crabSfx.loop = true;
crabSfx.volume = 0;

const walkingSfx = new Audio('walk.mp3');
walkingSfx.loop = true;
walkingSfx.volume = 0;

const playSfx = (audioPath, volume = 1) => {
    const audio = new Audio(audioPath);
    audio.volume = volume;
    audio.play();
};

startBtn.addEventListener('click', () => {
    if (mode !== 1) {
        mode = 1;
        startBtn.classList.add('active');
        
        startBtn.innerText = 'Running...';

        crabSfx.play();
        sizzleSfx.play();
        walkingSfx.play();

        const segmentConfig = [ ...game.segments ];

        gameLoopInterval = setInterval(async () => { 
            let result = game.gameLoop();
            
            if (result.success === true) {
                stopGame();
                
                const serverResult = await sendDataToServer(segmentConfig).then(res => res.json());
                if (serverResult.success === true) {
                    playSfx('win.mp3');
                    showWinDialog(serverResult);
                    if ((serverResult.completedLevels || []).length === 12) {
                        
                        // win condition

                        if (!localStorage.getItem('winState')) {
                            localStorage.setItem('winState', 'true');
                            
                            milestoneText.innerHTML = `
                                <p>Congratulations! You've completed all levels!</p>
                                <p>That said, there is one level even our best Elf Minders have struggled to complete.</p>
                                <p>It's <strong>A Real Pickle</strong>, to be sure.</p>
                                <p>We're not even sure it's solvable with our current tools.</p>
                                <p>I've added <strong>A Real Pickle</strong> to your level list on the main menu.</p>
                                <p>Can you give it a try?</p>
                            `;

                            milestoneBtn.innerText = 'Try A Real Pickle now';
                            milestoneCloseBtn.innerText = 'Try it Later';

                            milestoneBtn.addEventListener('click', () => {
                                window.location.href = `index.html?id=${rid}&level=${encodeURIComponent('A Real Pickle')}`;
                            });
                            milestoneDialog.classList.remove('hidden');
                            
                        }
                    } else if ((serverResult.completedLevels || []).length === 13) {
                        if (!localStorage.getItem('winMaster')) {
                            localStorage.setItem('winMaster', 'true');
                            
                            milestoneText.innerHTML = `
                                <p>Whoa!! You are a Elf Minding Master!</p>
                            `;

                            milestoneBtn.innerText = 'Main Menu';
                            milestoneBtn.addEventListener('click', () => {
                                window.location.href = `index.html?id=${rid}`;
                            });
                            milestoneDialog.classList.remove('hidden');
                            
                        }
                    }
                } else {
                    showErrorDialog(serverResult.message);
                }
            }
            
            if (game.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH > 999) {
                playSfx('lose.mp3');
                stopGame();
                showErrorDialog('Time limit exceeded.');
            }
        }, 40);
    }
});

trashBtn.addEventListener('click', () => {
    if (mode === 0) {
        game.segments = [];
        game.entities = [ ...level.entities ];
        saveSegmentsAndEntitiesToLocalStorage();
    }
});

resetBtn.addEventListener('click', () => {
    window.location.reload();
});

clearPathBtn.addEventListener('click', () => {
    game.segments = [];
});

clearEntitiesBtn.addEventListener('click', () => {
    game.entities = [];
});

function getColorProgress(progress) {
    // Ensure progress is between 0 and 1
    progress = Math.max(0, Math.min(1, progress));

    // Calculate RGB values
    // Green (0, 255, 0) to Red (255, 0, 0)
    let r = Math.round(progress * 255);
    let g = Math.round((1 - progress) * 255);
    let b = 0; // Blue stays at 0 for this transition

    // Convert RGB to hex
    return '#' + 
        ('0' + r.toString(16)).slice(-2) + 
        ('0' + g.toString(16)).slice(-2) + 
        ('0' + b.toString(16)).slice(-2);
}

const renderTimer = () => {
    ctx.save();
    ctx.fillStyle = '#2091a2';
    ctx.fillRect(0, rows * cellSize, canvas.width, 18);
    // ctx.fillStyle = 'green';
    ctx.fillStyle = getColorProgress(game.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH / timeLimit);
    ctx.fillRect(0, rows * cellSize, canvas.width - ((game.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH / timeLimit) * canvas.width), 18);

    ctx.fillStyle = `rgba(0,0,0,${(1 - (game.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH / timeLimit)) / 2})`;
    ctx.fillRect(0, rows * cellSize, canvas.width - ((game.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH / timeLimit) * canvas.width), 18);

    ctx.font = 'bold 15px Arial';
    ctx.fontWeight = 'bold';
    
    ctx.fillStyle = timeLimit - game.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH > 50 ? 'white' : 'red';
    const textXPos = canvas.width - ((game.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH / timeLimit) * canvas.width) - 150;

    ctx.fillText(`Time remaining: ${timeLimit - game.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH}`, textXPos < 2 ? 2 : textXPos, rows * cellSize + 14);
    ctx.restore();
};

const renderSegments = () => {
    ctx.save();
    ctx.strokeStyle = 'red';
    ctx.lineWidth = 4;
    ctx.setLineDash([6, 4]);
    ctx.lineDashOffset = -dashOffset;
    ctx.beginPath();
    game.segments.forEach(segment => {
        const [start, end] = segment;
        ctx.moveTo(start[0] * cellSize, start[1] * cellSize);
        ctx.lineTo(end[0] * cellSize, end[1] * cellSize);
    });
    // ctx.closePath();
    ctx.stroke();
    ctx.restore();

    if (game.hero.journey) {
        ctx.save();
        ctx.beginPath();
        ctx.strokeStyle = 'red';
        ctx.lineWidth = 4;
        ctx.moveTo(game.hero.journey[0][0] * cellSize, game.hero.journey[0][1] * cellSize);
        ctx.lineTo(game.hero.journey[1][0] * cellSize, game.hero.journey[1][1] * cellSize);
        ctx.stroke();
        ctx.restore();
    }
}

const renderHighlightCell = () => {
    if (highlightCell) {
        ctx.save();
        ctx.strokeStyle = 'yellow';
        ctx.strokeRect(highlightCell[0] * cellSize - cellSize, highlightCell[1] * cellSize - cellSize, bgTileSize, bgTileSize);
        ctx.restore();
    }
};

const clampInteger = (value, min, max) => Math.min(Math.max(value, min), max);

const renderEntities = () => {
    const blockers = game.entities.filter(entity => entity[2] === EntityTypes.BLOCKER);
    blockers.forEach(([x, y]) => {
        drawImageWithTransform(ImageAssets.blocker, x * cellSize - ImageAssets.blocker.width / 2, y * cellSize - ImageAssets.blocker.height / 2);
    });
    const crates = game.entities.filter(entity => entity[2] === EntityTypes.CRATE);
    crates.forEach(([x, y]) => {
        drawImageWithTransform(ImageAssets.crate, x * cellSize - ImageAssets.crate.width / 2, y * cellSize - ImageAssets.crate.height / 2);
    });
    const hazards = game.entities.filter(entity => entity[2] === EntityTypes.HAZARD);
    hazards.forEach(([x, y]) => {
        
        drawImageWithTransform(animationTimer < 25 ? ImageAssets.crab : ImageAssets.crab2, x * cellSize - ImageAssets.crab.width / 2, y * cellSize - ImageAssets.crab.height / 2);
    });
    const starts = game.entities.filter(entity => entity[2] === EntityTypes.START);
    starts.forEach(([x, y]) => {
        drawImageWithTransform(ImageAssets.startflag, x * cellSize - ImageAssets.startflag.width / 2, y * cellSize - ImageAssets.startflag.height / 2);
    });
    const ends = game.entities.filter(entity => entity[2] === EntityTypes.END);
    ends.forEach(([x, y]) => {
        // (img, x, y, rotation=0, xScale=1, yScale=1, origin={x: 0, y: 0}, opacity=1) 
        drawImageWithTransform(ImageAssets.finishflag, x * cellSize - ImageAssets.finishflag.width / 2, y * cellSize - ImageAssets.finishflag.height / 2, 0, 1, 1, { x: 0, y: 0 }, !game.entities.find(entity => entity[2] === EntityTypes.CRATE) ? 1 : .5);
    });
    const steams = game.entities.filter(entity => entity[2] === EntityTypes.STEAM);
    const graphic = `steam${clampInteger(Math.floor(animationTimer / (50 / 2)) + 1, 1, 2)}`;
    steams.forEach(([x, y]) => {
        drawImageWithTransform(ImageAssets[graphic], x * cellSize - ImageAssets[graphic].width / 2, y * cellSize - ImageAssets[graphic].height / 2);
    });
    const portals = game.entities.filter(entity => entity[2] === EntityTypes.PORTAL);
    portals.forEach(([x, y]) => {
        drawImageWithTransform(ImageAssets.portal, x * cellSize - ImageAssets.portal.width / 2, y * cellSize - ImageAssets.portal.height / 2);
    });
    const springs = game.entities.filter(entity => entity[2] === EntityTypes.SPRING);
    springs.forEach(([x, y]) => {
        drawImageWithTransform(ImageAssets.spring, x * cellSize - ImageAssets.spring.width / 2, -11 + y * cellSize - ImageAssets.spring.height / 2);
    });
}

const renderHero = () => {
    if (game.hero.journey && mode === 1) {
        const [start, end] = game.hero.journey;
        const dx = (end[0] - start[0]) * cellSize;
        const dy = (end[1] - start[1]) * cellSize;

        let x, y;

        if (!game.hero.airtime) {
            x = start[0] * cellSize + dx * game.hero.progress;
            y = start[1] * cellSize + dy * game.hero.progress;
        } else {
            const jumpProgress = 1 - (game.hero.airtime / game.hero.airtimeStatic);
            const jumpHeight = 500 * (game.hero.airtimeStatic / (7 * 10));
            // interpolate between this.hero.cell and this.hero.journey[0]
            const dx = game.hero.journey[0][0] - game.hero.cell[0];
            const dy = game.hero.journey[0][1] - game.hero.cell[1];
            
            x = game.hero.cell[0] * cellSize + (dx * cellSize * jumpProgress);

            // make hero jump
            y = game.hero.cell[1] * cellSize + (dy * cellSize * jumpProgress) - (Math.sin(jumpProgress * Math.PI) * jumpHeight);
        
        }
        game.hero.x = x;
        game.hero.y = y;
        console.groupEnd();
        drawImageWithTransform(ImageAssets[`elf${game.hero.face || ''}`], x - ImageAssets.elf.width / 2, y - ImageAssets.elf.height / 2 - 14);
    }
}

const drawLoop = () => {
    if (mode !== 2) {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        drawBackground();
        drawGridPoints();
        renderSegments();
        renderHighlightCell();
        renderEntities();
        renderHero();
        renderTimer();
        renderToolSelection();
        requestAnimationFrame(drawLoop);
    }
}

let animationTimer = 0;
let marchTimer;

function march() {
    
    dashOffset += 1;
    dashOffset = dashOffset >= 10 ? 0 : dashOffset;

    animationTimer += 1;
    animationTimer = animationTimer > 50 ? 0 : animationTimer;
    
    if (mode !== 2) marchTimer = setTimeout(march, 50);
}

if (mode !== -1) {
    march();
    drawLoop();
}

const sendDataToServer = async (segmentConfig) => {
    const data = {
        level: levelNum,
        entities: game.entities,
        segments: segmentConfig,
        clicks: game.clicks,
        rid,
    };

    stopGame();

    if (!isEditor) {
        const result = await fetch('/game/submit', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(data),
        });

        return result;
    }
}
```


## Levels
```javascript
HTTP/2 200 OK
Content-Type: text/javascript;charset=utf-8
Content-Length: 17010
Date: Thu, 05 Dec 2024 23:04:05 GMT
Via: 1.1 google
Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000

const Levels = {
    "Sandy Start": {
        "name": "Sandy Start",
        "entities": [
            [
                1,
                5,
                0
            ],
            [
                5,
                3,
                2
            ],
            [
                7,
                7,
                2
            ],
            [
                11,
                5,
                1
            ]
        ]
    },
    "Waves and Crates": {
        "name": "Waves and Crates",
        "entities": [
            [
                7,
                7,
                5
            ],
            [
                5,
                3,
                5
            ],
            [
                9,
                3,
                5
            ],
            [
                3,
                7,
                5
            ],
            [
                11,
                5,
                1
            ],
            [
                5,
                7,
                2
            ],
            [
                5,
                5,
                3
            ],
            [
                3,
                3,
                2
            ],
            [
                7,
                3,
                2
            ],
            [
                9,
                5,
                3
            ],
            [
                1,
                5,
                0
            ]
        ]
    },
    "Tidal Treasures": {
        "name": "Tidal Treasures",
        "entities": [
            [
                1,
                5,
                0
            ],
            [
                1,
                3,
                2
            ],
            [
                1,
                7,
                2
            ],
            [
                3,
                5,
                2
            ],
            [
                5,
                3,
                4
            ],
            [
                5,
                7,
                4
            ],
            [
                5,
                5,
                5
            ],
            [
                11,
                5,
                1
            ]
        ]
    },
    "Dune Dash": {
        "name": "Dune Dash",
        "entities": [
            [
                1,
                1,
                0
            ],
            [
                11,
                9,
                1
            ],
            [
                5,
                5,
                5
            ],
            [
                5,
                3,
                5
            ],
            [
                5,
                7,
                5
            ],
            [
                7,
                3,
                3
            ],
            [
                7,
                7,
                3
            ],
            [
                7,
                5,
                2
            ],
            [
                9,
                9,
                3
            ],
            [
                7,
                9,
                2
            ],
            [
                3,
                5,
                4
            ]
        ]
    },
    "Coral Cove": {
        "name": "Coral Cove",
        "entities": [
            [
                11,
                1,
                0
            ],
            [
                11,
                9,
                1
            ],
            [
                7,
                5,
                3
            ],
            [
                5,
                3,
                3
            ],
            [
                3,
                7,
                3
            ],
            [
                5,
                5,
                2
            ],
            [
                3,
                3,
                2
            ],
            [
                7,
                3,
                2
            ],
            [
                1,
                5,
                4
            ],
            [
                9,
                9,
                5
            ],
            [
                7,
                9,
                5
            ],
            [
                5,
                9,
                5
            ],
            [
                1,
                7,
                2
            ]
        ]
    },
    "Shell Seekers": {
        "name": "Shell Seekers",
        "entities": [
            [
                1,
                9,
                0
            ],
            [
                11,
                3,
                1
            ],
            [
                11,
                7,
                5
            ],
            [
                7,
                3,
                5
            ],
            [
                5,
                3,
                5
            ],
            [
                1,
                1,
                5
            ],
            [
                1,
                5,
                5
            ],
            [
                1,
                7,
                5
            ],
            [
                7,
                9,
                5
            ],
            [
                11,
                1,
                5
            ],
            [
                5,
                7,
                5
            ],
            [
                5,
                9,
                2
            ],
            [
                1,
                3,
                2
            ],
            [
                5,
                1,
                2
            ],
            [
                9,
                5,
                2
            ],
            [
                11,
                9,
                2
            ],
            [
                5,
                5,
                2
            ],
            [
                7,
                5,
                4
            ],
            [
                9,
                9,
                3
            ],
            [
                3,
                5,
                3
            ],
            [
                7,
                1,
                3
            ]
        ]
    },
    "Palm Grove Shuffle": {
        "name": "Palm Grove Shuffle",
        "entities": [
            [
                1,
                1,
                0
            ],
            [
                7,
                3,
                3
            ],
            [
                7,
                7,
                3
            ],
            [
                5,
                1,
                3
            ],
            [
                5,
                9,
                3
            ],
            [
                3,
                5,
                3
            ],
            [
                3,
                7,
                2
            ],
            [
                7,
                9,
                2
            ],
            [
                7,
                1,
                2
            ],
            [
                3,
                3,
                2
            ],
            [
                11,
                9,
                1
            ],
            [
                11,
                5,
                3
            ]
        ]
    },
    "Tropical Tangle": {
        "name": "Tropical Tangle",
        "entities": [
            [
                11,
                1,
                3
            ],
            [
                9,
                3,
                3
            ],
            [
                9,
                5,
                3
            ],
            [
                11,
                7,
                3
            ],
            [
                11,
                5,
                1
            ],
            [
                7,
                5,
                4
            ],
            [
                5,
                3,
                5
            ],
            [
                5,
                5,
                5
            ],
            [
                3,
                3,
                5
            ],
            [
                3,
                5,
                5
            ],
            [
                5,
                1,
                2
            ],
            [
                1,
                1,
                2
            ],
            [
                1,
                3,
                2
            ],
            [
                1,
                5,
                2
            ],
            [
                1,
                7,
                2
            ],
            [
                5,
                7,
                2
            ],
            [
                3,
                1,
                3
            ],
            [
                3,
                7,
                3
            ],
            [
                11,
                9,
                0
            ]
        ]
    },
    "Crate Caper": {
        "name": "Crate Caper",
        "entities": [
            [
                11,
                1,
                0
            ],
            [
                7,
                3,
                3
            ],
            [
                7,
                1,
                3
            ],
            [
                3,
                3,
                3
            ],
            [
                3,
                1,
                3
            ],
            [
                7,
                7,
                3
            ],
            [
                7,
                9,
                3
            ],
            [
                3,
                9,
                3
            ],
            [
                3,
                7,
                3
            ],
            [
                1,
                5,
                3
            ],
            [
                3,
                5,
                2
            ],
            [
                5,
                1,
                2
            ],
            [
                5,
                9,
                2
            ],
            [
                7,
                5,
                2
            ],
            [
                9,
                5,
                3
            ],
            [
                9,
                9,
                1
            ]
        ]
    },
    "Shoreline Shuffle": {
        "name": "Shoreline Shuffle",
        "entities": [
            [
                1,
                1,
                3
            ],
            [
                3,
                3,
                3
            ],
            [
                5,
                1,
                3
            ],
            [
                7,
                3,
                3
            ],
            [
                9,
                1,
                3
            ],
            [
                11,
                3,
                3
            ],
            [
                3,
                5,
                3
            ],
            [
                7,
                5,
                3
            ],
            [
                11,
                5,
                3
            ],
            [
                1,
                3,
                2
            ],
            [
                5,
                3,
                2
            ],
            [
                9,
                3,
                2
            ],
            [
                1,
                9,
                0
            ],
            [
                11,
                9,
                1
            ],
            [
                5,
                9,
                4
            ],
            [
                11,
                7,
                4
            ]
        ]
    },
    "Beachy Bounty": {
        "name": "Beachy Bounty",
        "entities": [
            [
                1,
                5,
                0
            ],
            [
                11,
                5,
                1
            ],
            [
                5,
                3,
                3
            ],
            [
                5,
                7,
                3
            ],
            [
                3,
                5,
                4
            ],
            [
                7,
                1,
                5
            ],
            [
                7,
                3,
                5
            ],
            [
                9,
                1,
                5
            ],
            [
                5,
                5,
                2
            ],
            [
                1,
                1,
                2
            ],
            [
                9,
                3,
                4
            ],
            [
                7,
                7,
                4
            ],
            [
                9,
                9,
                2
            ]
        ]
    },
    "Driftwood Dunes": {
        "name": "Driftwood Dunes",
        "entities": [
            [
                5,
                3,
                2
            ],
            [
                7,
                5,
                2
            ],
            [
                5,
                5,
                3
            ],
            [
                7,
                3,
                3
            ],
            [
                1,
                9,
                0
            ],
            [
                11,
                9,
                1
            ],
            [
                11,
                7,
                4
            ],
            [
                1,
                5,
                5
            ],
            [
                1,
                3,
                5
            ]
        ]
    },
    "A Real Pickle": {
        "name": "A Real Pickle",
        "entities": [
            [
                1,
                1,
                0
            ],
            [
                11,
                9,
                1
            ],
            [
                9,
                9,
                3
            ],
            [
                11,
                7,
                3
            ],
            [
                7,
                9,
                3
            ],
            [
                5,
                9,
                3
            ],
            [
                3,
                9,
                3
            ],
            [
                1,
                9,
                3
            ],
            [
                11,
                5,
                3
            ],
            [
                9,
                5,
                3
            ],
            [
                7,
                3,
                3
            ],
            [
                5,
                1,
                3
            ],
            [
                3,
                3,
                3
            ],
            [
                1,
                3,
                3
            ],
            [
                9,
                1,
                2
            ],
            [
                5,
                3,
                2
            ],
            [
                3,
                7,
                2
            ],
            [
                7,
                7,
                2
            ],
            [
                11,
                3,
                3
            ],
            [
                11,
                1,
                3
            ]
        ],
    }
}

if (typeof module !== 'undefined' && module.exports) {
    module.exports = Levels;
}
```


## Guide
```javascript
HTTP/2 200 OK
Content-Type: text/javascript;charset=utf-8
Content-Length: 30471
Date: Thu, 05 Dec 2024 23:26:40 GMT
Via: 1.1 google
Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000


const cellSize = 800 / 12;

const EntityTypes = {
    START: 0,
    END: 1,
    CRATE: 2,
    BLOCKER: 3,
    HAZARD: 4,
    STEAM: 5,
    PORTAL: 6,
    SPRING: 7,
};

const EntityTypesRef = {
    0: 'start',
    1: 'end',
    2: 'crate',
    3: 'blocker',
    4: 'hazard',
    5: 'steam',
    6: 'portal',
    7: 'spring',
};

class Game {
    constructor(data, Levels) {

        if (typeof data.level === 'undefined' || !data.segments || !data.entities || !data.clicks) {
            throw new Error('Invalid game data [Earth]');
        }

        if (!Levels[data.level]) {
            throw new Error('Invalid game data [Wind]');
        }

        if (!this.isObjectAnArray(data.segments) || !this.isObjectAnArray(data.entities) || !this.isObjectAnArray(data.clicks)) {
            throw new Error('Invalid game data [Water]');
        }

        if (data.segments.length > 60) {
            throw new Error('Invalid game data [Fire]');
        }

        data.segments.forEach(segment => {
            if (!this.isSegmentValid(segment)) {
                throw new Error('Invalid game data [CAPTAIN PLANET!]');
            }
        });

        
        
        this.entities = data.entities;
        this.segments = data.segments;
        this.level = data.level;
        this.clicks = data.clicks;
        this.path = [];
        this.hero = {};
        this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH = 0;
        
        const level = Levels[data.level];
        const levelEntities = level.entities;
        
        this.entities = [ ...this.entities.filter(d => ~[EntityTypes.PORTAL, EntityTypes.SPRING].indexOf(d[2])), ...levelEntities ];
        
        this.dedupeSegments();
        this.disappointHackers();

        if (this.segments.length !== data.segments.length) {
            throw new Error('Invalid game data [Heart]');
        }
    }
    isCellOneWay([x, y]) {
        return this.entities.some(entity => {
            return entity[0] === x && entity[1] === y && ~[EntityTypes.PORTAL, EntityTypes.START, EntityTypes.END].indexOf(entity[2]);
        });
    }
    isPointInSegment(point, segment) {
        const [start, end] = segment;
        const [x, y] = point;
        return start[0] === x && start[1] === y || end[0] === x && end[1] === y;
    }
    isPointInAnySegment(point) {
        return this.segments.some(segment => {
            return this.isPointInSegment(point, segment);
        });
    }
    getCellMidpoint([x, y]) {
        return [ x % 2 === 0 ? x + 1 : x, y % 2 === 0 ? y + 1 : y ]
    }
    getCellByPoint([x, y]) {
        const subcell = [ Math.floor(x / cellSize), Math.floor(y / cellSize) ];
        return [ subcell[0] % 2 === 0 ? subcell[0] + 1 : subcell[0], subcell[1] % 2 === 0 ? subcell[1] + 1 : subcell[1] ];
    }
    getSegmentMidpoint([start, end]) {
        return [ (start[0] + end[0]) / 2, (start[1] + end[1]) / 2 ];
    }
    pythag([x1, y1], [x2, y2]) {
        return Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
    }
    isObjectAnArray(obj) {
        return Object.prototype.toString.call(obj) === '[object Array]';
    }
    isSegmentValid(segment) {
        const [start, end] = segment;
        if (!start || !end) return false;
        return this.pythag(start, end) === 1;
    }
    dedupeSegments() {
        const deduped = [];
        this.segments.forEach(segment => {
            const [start, end] = segment;
            const [startX, startY] = start;
            const [endX, endY] = end;
            const existing = deduped.find(([dedupedStart, dedupedEnd]) => {
                const [dedupedStartX, dedupedStartY] = dedupedStart;
                const [dedupedEndX, dedupedEndY] = dedupedEnd;
                return dedupedStartX === startX && dedupedStartY === startY && dedupedEndX === endX && dedupedEndY === endY;
            });
            if (!existing) {
                deduped.push(segment);
            }
        });
        this.segments = deduped;
    }
    disappointHackers() {
        this.segments = this.segments.filter(segment => {
            const midpoint = this.getSegmentMidpoint(segment);
            const cell = this.getCellByPoint(midpoint.map(d => d * cellSize));
            return !this.entities.some(entity => entity[2] === EntityTypes.BLOCKER && entity[0] === cell[0] && entity[1] === cell[1]);
        });
    };
    getEntitiesByType(type) {
        return this.entities.filter(entity => entity[2] === type);
    }
    getEntitiesAtCell([x, y]) {
        return this.entities.filter(entity => entity[0] === x && entity[1] === y);
    }
    removeEntity(cell, type) {
        this.entities = this.entities.filter(entity => entity[0] !== cell[0] || entity[1] !== cell[1] || entity[2] !== type);
    }
    getNextJourney() {
        const [ currentJourneyStart, currentJourneyEnd ] = this.hero.journey;

        const potentialPaths = this.segments.filter(([ potentialStart, potentialEnd ]) => {
            return potentialStart[0] === currentJourneyEnd[0] && potentialStart[1] === currentJourneyEnd[1] || 
                    potentialEnd[0] === currentJourneyEnd[0] && potentialEnd[1] === currentJourneyEnd[1];
        });

        if (potentialPaths.length === 1) {
            if (potentialPaths[0][0][0] === currentJourneyEnd[0] && potentialPaths[0][0][1] === currentJourneyEnd[1]) {
                return potentialPaths[0];
            } else {
                return [ potentialPaths[0][1], potentialPaths[0][0] ];
            }
        } else {
            // favor the path that doesn't lead back to the currentJourneyStart
            const ideal = potentialPaths.find(([ potentialStart, potentialEnd ]) => {
                return !this.isPointInSegment(currentJourneyStart, [ potentialStart, potentialEnd ]);
            });
    
            if (ideal) {
                if (ideal[0][0] === currentJourneyEnd[0] && ideal[0][1] === currentJourneyEnd[1]) return ideal;
    
                return [ ideal[1], ideal[0] ];
            }
    
            const fallback = potentialPaths.find(([ potentialStart, potentialEnd ]) => {
                return potentialEnd[0] === currentJourneyStart[0] && potentialEnd[1] === currentJourneyStart[1];
            });
            return [ fallback[0], fallback[1] ];
        }
    }
    isNearHazard() {
        const [x, y] = this.hero.journey[0];
        const [x2, y2] = this.hero.journey[1];
        const midway = [ (x + x2) / 2, (y + y2) / 2 ];
        const hazards = this.entities.filter(entity => entity[2] === EntityTypes.HAZARD);
        const heroCell = this.getCellMidpoint(this.hero.journey[0]);
    
        return hazards.find(hazard => {
            return this.pythag(midway, hazard) < 2.09;
        })
    }
    getSegmentsThatTouchPoint(point) {
        return this.segments.filter(segment => {
            return this.isPointInSegment(point, segment);
        });
    }
    getSegmentInfo(segment) {
        const [start, end] = segment;
        const [startX, startY] = start;
        const [endX, endY] = end;
        const segMidpoint = this.getSegmentMidpoint(segment);
    
        const point = this.getCellByPoint(segMidpoint.map(d => d * cellSize));
        
        const segmentIndex = this.getIndexOfSegment(segment);
    
        const isHorizontal = startY === endY;
        const isVertical = startX === endX;
    
        const isLeftToMid = isHorizontal && this.isPointInSegment([ point[0] - 1, point[1] ], segment);
        const isRightToMid = isHorizontal && this.isPointInSegment([ point[0] + 1, point[1] ], segment);
        const isTopToMid = isVertical && this.isPointInSegment([ point[0], point[1] - 1], segment);
        const isBottomToMid = isVertical && this.isPointInSegment([ point[0], point[1] + 1], segment);
    
        return {
            segmentIndex,
            isHorizontal,
            isVertical,
            isLeftToMid,
            isRightToMid,
            isTopToMid,
            isBottomToMid,
        };
    }
    rotateSegments(cell, addClick = false) {
        const point = this.getCellMidpoint(cell);

        const toRotate = this.getSegmentsThatTouchPoint(point);
        
        if (toRotate.length) {
            
            const newSegments = [];
            toRotate.forEach(segment => {
                // does segment span from left side to midpoint?

                const [start, end] = segment;
                const [startX, startY] = start;
                const [endX, endY] = end;
                            
                const {
                    segmentIndex,
                    isLeftToMid,
                    isRightToMid,
                    isTopToMid,
                    isBottomToMid,
                } = this.getSegmentInfo(segment);
                
                if (isLeftToMid) {
                    newSegments.push([point, [point[0], point[1] - 1]]);
                } else if (isRightToMid) {
                    newSegments.push([point, [point[0], point[1] + 1]]);
                } else if (isTopToMid) {
                    newSegments.push([point, [ point[0] + 1, point[1]]]);
                } else if (isBottomToMid) {
                    newSegments.push([point, [ point[0] - 1, point[1]]]);
                }
                this.segments.splice(segmentIndex, 1);
            });
            this.segments = this.segments.concat(newSegments);
            if (addClick) {
                this.addClick(cell);
            }
        }
    }
    addClick(cell) {
        this.clicks.push([cell, this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH + 1]);
    }
    getIndexOfSegment([start, end]) {
        return this.segments.findIndex(segment => {
            return segment[0][0] === start[0] && segment[0][1] === start[1] && segment[1][0] === end[0] && segment[1][1] === end[1];
        })
    }
    getSpringTarget(springCell) {
        const journey = this.hero.journey;
        const dy = journey[1][1] - journey[0][1];
        const dx = journey[1][0] - journey[0][0];

        let nextPoint = [ springCell[0], springCell[1] ];
        let entityHere;
        let searchLimit = 15;
        let searchIndex = 0;
        let validTarget;

        do {
            searchIndex += 1;
            nextPoint = [ nextPoint[0] + dx, nextPoint[1] + dy ];
            
            entityHere = this.entities.find(entity => 
                ~[
                    EntityTypes.PORTAL,
                    EntityTypes.SPRING,
                ].indexOf(entity[2]) &&
                searchIndex &&
                entity[0] === nextPoint[0] &&
                entity[1] === nextPoint[1]);
            
            if (searchIndex >= searchLimit) {
                break;
            }

            validTarget = this.isPointInAnySegment(nextPoint) || entityHere;
        } while (!validTarget);

        if (this.isPointInAnySegment(nextPoint) || entityHere) {
            if (entityHere) return this.segments[0][0]; // fix this
            return nextPoint;
        } else {
            return;
        }        
    }
    displayEntity(entity) {
        console.log(`ENTITY [${EntityTypesRef[entity[2]]}]: ${entity[0]}, ${entity[1]}`);
    }
    processEntityInteractions(position) {
        const cellEntities = this.getEntitiesAtCell(position);

        cellEntities.forEach(this.displayEntity);
        
        if (cellEntities.length === 0) return;

        let result;

        cellEntities.forEach(entity => {
            switch(EntityTypesRef[entity[2]]) {
                case 'crate':
                    if (typeof playSfx !== 'undefined') playSfx('pickup.mp3');
                    this.path.push(`${this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH}: REMOVED CRATE`);
                    this.removeEntity(this.hero.journey[1], EntityTypes.CRATE);
                    break;
                case 'spring':
                    const target = this.getSpringTarget(position);
    
                    if (target) {
                        const startSegments = this.getSegmentsThatTouchPoint(target);
                        if (!startSegments.length) {
                            return;
                        }
                        if (typeof playSfx !== 'undefined') playSfx('spring.mp3');
                        let newJourney = startSegments[0];
                        newJourney = newJourney[0][0] === target[0] && newJourney[0][1] === target[1] ? newJourney : [ newJourney[1], newJourney[0] ];
                        this.hero.progress = 0;
                        this.hero.newJourney = newJourney;
                        this.hero.airtime = Math.floor(this.pythag(position, newJourney[0]) * 5);
                        this.hero.airtimeStatic = this.hero.airtime;     
                    }
                    break;
                case 'end':
                    if (!this.entities.find(entity => entity[2] === EntityTypes.CRATE)) {
                        result = {
                            success: true,
                            data: {
                                segments: this.segments.length,
                                clicks: this.clicks.length,
                                level: this.level,
                                ticks: this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH,
                                goodScore: calculateScore(this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH, this.segments.length, this.clicks.length),
                                evilScore: calculateScore2(this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH, this.segments.length, this.clicks.length),
                            }
                        };
                        
                    }
                    break;
                default:
            }
        });
        return result;
    }
    hasStartSegments() {
        return this.getSegmentsThatTouchPoint(this.entities.find(entity => entity[2] === EntityTypes.START)).length > 0;
    }
    gameLoop() {
        const click = this.clicks.find(([cell, ts]) => ts === this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH);
        
        if (this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH === 0 && this.getEntitiesByType(EntityTypes.SPRING).length > 2 && typeof process === 'undefined') {
            console.log(whyCantIHoldAllTheseSprings());
        }
        
        if (click) {
            this.path.push(`${this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH}: CLICKED ${click[0]}`);
            this.rotateSegments(click[0]);
        }
        if (typeof this.hero.cell === 'undefined') {
            // initialize hero
            this.hero.cell = this.getEntitiesByType(EntityTypes.START)[0];
            this.hero.x = this.hero.cell[0] * cellSize;
            this.hero.y = this.hero.cell[1] * cellSize; 
            this.hero.progress = 0;
            const startSegments = this.getSegmentsThatTouchPoint(this.entities.find(entity => entity[2] === EntityTypes.START));
            if (!startSegments.length) {
                console.log('no start segment found');
                return;
            }
            const startCell = this.hero.cell;
            this.hero.journey = startSegments[0]
            this.hero.journey = this.hero.journey[0][0] === startCell[0] && this.hero.journey[0][1] === startCell[1] ? this.hero.journey : [ this.hero.journey[1], this.hero.journey[0] ];
        } else {
            if (typeof walkingSfx !== 'undefined') walkingSfx.volume = this.hero.airtime > 0 ? 0 : 1;
            if (this.hero.airtime > 0) {
                this.hero.airtime -= 1;
                if (this.hero.airtime === 0) {
                    const currentCellEntities = this.getEntitiesAtCell(this.hero.journey[0]);
                    if (currentCellEntities.some(entity => entity[2] === EntityTypes.CRATE)) {
                        this.path.push(`${this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH}: REMOVED CRATE`);
                        this.removeEntity(this.hero.journey[0], EntityTypes.CRATE);
                    }
                }
            } else {
                const midpoint = this.getCellMidpoint(this.hero.journey[0]);
                // const cellGeometry = getCellGeometry(midpoint);
                const nearHazard = this.isNearHazard();
                const cellEnts = this.getEntitiesAtCell(midpoint);
                const isHot = cellEnts.some(entity => entity[2] === EntityTypes.STEAM);
                this.hero.face = nearHazard ? '_nervous' : (isHot ? '_hot' : '');
                
                if (typeof crabSfx !== 'undefined') crabSfx.volume = nearHazard ? 1 : 0;
                if (typeof sizzleSfx !== 'undefined') sizzleSfx.volume = isHot ? 1 : 0;

                if (this.hero.progress === 0) this.processEntityInteractions(this.hero.journey[0]);

                this.hero.progress += nearHazard ? .035 : ( isHot ? 0.15 : 0.075 );
                let newJourney;
                if (this.hero.progress >= 1) {

                    const result = this.processEntityInteractions(this.hero.journey[1]);
                    if (result) return result;

                    this.hero.progress = 0;

                    if (this.hero.newJourney) {
                        this.hero.cell = this.hero.journey[1];
                        newJourney = this.hero.newJourney;

                        this.hero.newJourney = null;
                    } else {
                        this.hero.cell = this.getCellMidpoint(this.hero.journey[1]);
                        newJourney = this.getNextJourney();
                    }
                    const currentCellEntities = this.getEntitiesAtCell(this.hero.journey[1]);
                    this.path.push(`${this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH}: ${this.hero.journey[1]}`);
                    if (currentCellEntities.some(entity => entity[2] === EntityTypes.PORTAL)) {
                        const allPortals = this.getEntitiesByType(EntityTypes.PORTAL);
                        
                        if (allPortals.length !== 2) return;
                        const otherPortal = allPortals.find(portal => {
                            return portal[0] !== this.hero.journey[1][0] || portal[1] !== this.hero.journey[1][1];
                        });

                        if (!otherPortal) return;
                        const startSegments = this.getSegmentsThatTouchPoint(otherPortal);
                        if (!startSegments.length) {
                            console.log('no start segment found');
                            return;
                        }
                        if (typeof playSfx !== 'undefined') playSfx('pop.mp3');
                        newJourney = startSegments[0];
                        newJourney = newJourney[0][0] === otherPortal[0] && newJourney[0][1] === otherPortal[1] ? newJourney : [ newJourney[1], newJourney[0] ];
                    } 
                    if (newJourney) {
                        this.hero.journey = newJourney;
                    } else {
                        this.hero.journey = [ this.hero.journey[1], this.hero.journey[0] ];
                    }
                } 
            }
        }
        this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH += 1;
        return {
            success: false,
        }
    }
    solve() {
        return new Promise((resolve, reject) => {
            let result;
            do {
                result = this.gameLoop();
                if (this.EMMEHGERDTICKSTHERESTICKSEVERYWHEREARHGHAHGREHUHGHH > 999) {
                    return reject({
                        success: false,
                        reason: 'timeout',
                    });
                }
            } while (!(result || {}).success);
            if (result.success) {
                return resolve(result);
            }
        });
    }
}

if (typeof module !== 'undefined' && module.exports) {
    module.exports = {
        Game,
        EntityTypes,
    };
}


let calculateScore = (ticks, segments, clicks) => {
    // Base score
    let baseScore = 1000;

    // Ticks penalty: More ticks lead to a greater penalty
    let ticksPenalty = ticks * 1; // 1 point deducted per tick

    // Segments penalty: More segments lead to a greater penalty
    let segmentsPenalty = segments * 5; // 5 points deducted per segment

    // Final score calculation
    let score = baseScore - ticksPenalty - segmentsPenalty;

    // Ensure score doesn't go below 0
    score = Math.max(score, 0);

    return score;
}

let calculateScore2 = (ticks, segments, clicks) => {
    // Maximum possible score
    let maxScore = 1000;

    // Score gain: reward for more ticks and segments
    let ticksScore = ticks * .5;  // Each tick adds 1.5 points
    let segmentsScore = segments * 10;  // Each segment adds 15 points

    // Clicks penalty: Penalize heavily for clicks
    let clicksPenalty = clicks * 250;  // Each click subtracts 25 points

    // Calculate the total score
    let score = ticksScore + segmentsScore - clicksPenalty;

    // Ensure the score is within the 0 to 1000 range
    score = Math.min(Math.max(score, 0), maxScore);

    return score;
}

const whyCantIHoldAllTheseSprings = () => `

                                     WHY CAN'T I HOLD ALL THESE SPRINGS??


                                                                                                                                     
                                                 .--======-                                                          
                                             --              --.                                                     
                                          -=                     =:                                                  
                                        .=                         -                                                 
                                       -.                            -                                               
                                      ::                             .:                                              
                                      :                               :.                                             
                                     +                                 -                                             
                                    .-                                 :                                             
                                    ::                                 .-                                            
                                    ..                                  =                                            
                                    :                 :.                =:                                           
                                    :                                  =:  -                                         
                                     =                                *    -                                         
                                     ..       :        .:. ..              -                                         
                                      .                                   :.                                         
                 =.   .++:  ..        .   .                 :            =                                           
               +  *=.       =         -       .         *@@ .+          #                                            
              -+-     .:-**--::::     =               :+#%*-.          .:                                            
            :-   -+.            -      = .@@# *                        =                                             
        .#   .*               :-       .: #+:                          :                                             
      +   +=     -#.            -        =       :                     -                                             
     #        .=                =        .      +                      -.             .=++-         :-               
     =      -.          .++=-=+-         .     =        -                    ..:----    ...           ::             
     +   .-     :=.      : ..-+%+         +    :-   :: ..                           -***+++++=          =            
      -      .+        ..-::   *%:--.      +     -          .-*:                  .++*++++----::.       ..           
      .:   #       =%:.-:.    :*#:=+%=      #           .***+--=::--:..  .-:      :++*-::----:::....:    =           
       #      .+.   -.::    ..=#=: .*#.      .=  :+**:.+**==+:...:::::::::-. +:  :.-++.          .::-... +           
       +           .:.:    .--*... .#+-:=**-   -.     -+*+#+-.  -          .-=#. :.*:+*=..          .:- - -          
       =        ++=-.:    ..-*-..  ++::---*%- :=-.    -++*:    ..           =%#. :.-*-:=*+-.....      .-.+ -.        
       =       :*==.-.   ..-*:..  =:.-...:=%=....::   .:++++. :-......-===*#%+. .=:--+..::-+*+=---..   =#*. :        
       +       +*=*=-.  ..-*::. -+...     =#:.=+*%-+  . --:-++*********=:.-::**.:=..:=+:  :... :+**###%@*.   #       
       +       **=#=#   -*+:.-=+= ..     -*.-.   ##.   :..*+--:.......:-:   .*#:::  .:. =+.    :-:....:.      -      
       +       -***+=-+*+:-: ==..:.     =-.:     *#:.=#+::--===:         .-+%#::.+=+-...:..-==.     .+#=      :      
       +        ++++++-:===+=..:.-    .+:.:    .=*-:..:##-..:.....:::::::-=-:..:-.=.    ..:....:+###%#-        *     
       =          .-.:--:.:-:.:=:    ==...    .-+.:   .+%:+#+.-..::::...:---:=+:.:-=.         :-::...           :    
       ..         +  -...-=.  .+- .+-.::     .=- .  .:=**-==*%==            -+#- .:.:=:         .--=            -    
        :         *  -   . -****-.-::-.:    .+...  :: -=.-:.=%=**.          +%#=+-..::.-*=:.     .+#:            #   
        -         *  -     ..:::::. .=-.  :=..:  ... .:..+-.=%=+%========+#%*::=      .:.. :*#**#%%-              .  
         =        :  =         ..=+-:=====..:.  ... -....=- +.-=*-........--.-=-=.   +    ..:::::.                :  
         *        ..:            ......-::.--. .-:=:..  . =+.:-=.:       . =-=#=.:=-..--      :-*                 -  
         :.        -.                 .==:.-=. -+:..     -= ::::.        ::-+*%-:...=+=...    :+#.                =  
          .        =.                  .  ....=-:.      -:.:==:.-.:.... .-=+%#:    ... .=**+*#%#:                 .: 
          :        -                   . .:..+=-+.   -=..:+.=.:-..........:::-=--:..    .:::::.                   .- 
          :        .=  = .:           .=   .=. .....--....=:.--:      .:.+=..=*+- ..::.    +==.                    + 
          :         -   =  .-         .:     =  ...-+=-:--::=.   ....:.:.:.  =%=.:-.  .=.. :*#.                    + 
          -         :  ==    +.        :=     :-  ...::...::--==-......=:-==#%-  ....:=**#%%+.                     + 
          -          . .=:     +       .:.:     *        .-=--:...-=========:-*:      .:---+-.                     + 
          -             -+      -       :.*      =:      : +-     ...:---. -:-*+        :-** -                     + 
          -             :=..     .+       *        +     .:.:+         .:=- .*%-.-+==--=*%+. =                     + 
          -              .- :=      :=++==.         .-   ..+:-==-.      .-=##+=+.....-===*-  :==:                  + 
          -               .-   =                      -.  . = ..............=*#-:-===+##*=--.                      + 
          -                =-%=                        -:  .:.-.         .-+#+. .:.:=-:                           -  
          -                 - :*.                       -    ..:..::--:.. .-. :==*                                :  
           =                 : - .+                      .-        .*+.                                         =    
            =                = *    .=                      -+====:                                            :.    
             =               = -.      --                                                                     :      
              :              =  :          .---:::                                                           =       
              =              =  -                   .==..                                                  +         
               -                :                            .+*+.                                      :=           
                -.             :.                                        ::---=+**+-.               -#:              
                 ..            *                                                           :                         
                  ::           #                                                           -                         
                   :          +:                                                          :.                         
                    :.      :-.                                                           =                          
                              :                                                           .                          
                              :                                                          -                           
                              -                                                         .-                           
                              =                                                         -                            
                              -                                                        ..                            
                             -                                                         +                                          
`;
```