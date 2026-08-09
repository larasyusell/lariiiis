# Cómo contribuir al Base Guild

1. Sé respetuoso y colaborativo
2. Comparte conocimiento sobre el ecosistema Base
3. Propón ideas de eventos, herramientas o contenido
4. Ayuda a nuevos miembros a dar sus primeros pasos en Base

Toda contribución, por pequeña que sea, suma.

# Base Guild Resources

Listado curado de recursos, herramientas y links útiles para builders y miembros del Guild de Base.

Base es una L2 de Ethereum orientada a la adopción masiva. Este repositorio sirve como biblioteca viva de todo lo necesario para navegar y construir en su ecosistema.

# Visión de los eventos del Guild

Queremos que Base sea el lugar donde más fácil sea empezar en Web3.

Nuestros eventos buscan:
- Enseñar desarrollo on-chain de forma práctica
- Conectar builders locales y remotos
- Mostrar casos de uso reales
- Generar colaboraciones entre miembros

Todo con el espíritu open-source y colaborativo de Base.

# Base Guild Content

Repositorio de contenido del Guild de Base.

Aquí coordinamos la creación de material educativo, divulgativo y de marketing sobre el ecosistema Base: hilos, artículos, videos, memes y más.

# Por qué el contenido es clave en Base

Base tiene una ventaja única: el respaldo de Coinbase y un fuerte enfoque en onboarding de usuarios reales.

El contenido de calidad ayuda a:
- Explicar qué es Base de forma simple
- Mostrar casos de uso concretos
- Atraer a developers y usuarios no-crypto
- Posicionar al guild como referencia en el ecosistema

Educar es una de las formas más poderosas de contribuir.
# Pilares de contenido

1. Explicaciones simples de qué es Base
2. Tutoriales de desarrollo
3. Casos de uso y proyectos destacados
4. Actualizaciones del ecosistema
5. Historias de builders del guild

Todo el contenido debe ser claro, útil y orientado a la acción.
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract HelloBase {
    string public greeting = "Hello Base";
    address public lastSender;

    event GreetingChanged(string newGreeting, address indexed by);

    function setGreeting(string calldata newGreeting) external {
        greeting = newGreeting;
        lastSender = msg.sender;
        emit GreetingChanged(newGreeting, msg.sender);
    }

    function getGreeting() external view returns (string memory) {
        return greeting;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TipJar {
    address public owner;
    uint256 public totalTips;

    event Tipped(address indexed from, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    function tip() external payable {
        require(msg.value > 0, "Must send ETH");
        totalTips += msg.value;
        emit Tipped(msg.sender, msg.value);
    }

    function withdraw() external {
        require(msg.sender == owner, "Not owner");
        uint256 amount = address(this).balance;
        (bool success, ) = owner.call{value: amount}("");
        require(success, "Withdraw failed");
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleBank {
    mapping(address => uint256) public balances;

    event Deposited(address indexed user, uint256 amount);
    event Withdrawn(address indexed user, uint256 amount);

    function deposit() external payable {
        require(msg.value > 0, "Must send ETH");
        balances[msg.sender] += msg.value;
        emit Deposited(msg.sender, msg.value);
    }

    function withdraw(uint256 amount) external {
        require(balances[msg.sender] >= amount, "Insufficient balance");
        balances[msg.sender] -= amount;
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success, "Withdraw failed");
        emit Withdrawn(msg.sender, amount);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ArrayStore {
    uint256[] public numbers;

    event NumberAdded(uint256 number, uint256 index);

    function addNumber(uint256 number) external {
        numbers.push(number);
        emit NumberAdded(number, numbers.length - 1);
    }

    function getLength() external view returns (uint256) {
        return numbers.length;
    }

    function getNumber(uint256 index) external view returns (uint256) {
        require(index < numbers.length, "Index out of bounds");
        return numbers[index];
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract AccessControl {
    address public admin;
    mapping(address => bool) public moderators;

    event AdminChanged(address indexed newAdmin);
    event ModeratorAdded(address indexed account);
    event ModeratorRemoved(address indexed account);

    constructor() {
        admin = msg.sender;
    }

    modifier onlyAdmin() {
        require(msg.sender == admin, "Not admin");
        _;
    }

    function changeAdmin(address newAdmin) external onlyAdmin {
        require(newAdmin != address(0), "Invalid address");
        admin = newAdmin;
        emit AdminChanged(newAdmin);
    }

    function addModerator(address account) external onlyAdmin {
        moderators[account] = true;
        emit ModeratorAdded(account);
    }

    function removeModerator(address account) external onlyAdmin {
        moderators[account] = false;
        emit ModeratorRemoved(account);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract EtherWallet {
    address payable public owner;

    event Deposited(address indexed from, uint256 amount);
    event Withdrawn(address indexed to, uint256 amount);

    constructor() {
        owner = payable(msg.sender);
    }

    receive() external payable {
        emit Deposited(msg.sender, msg.value);
    }

    function withdraw(uint256 amount) external {
        require(msg.sender == owner, "Not owner");
        require(address(this).balance >= amount, "Insufficient balance");
        (bool success, ) = owner.call{value: amount}("");
        require(success, "Withdraw failed");
        emit Withdrawn(owner, amount);
    }

    function getBalance() external view returns (uint256) {
        return address(this).balance;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimplePoll {
    string public question;
    uint256 public optionA;
    uint256 public optionB;
    mapping(address => bool) public hasVoted;

    event Voted(address indexed voter, bool choseA);

    constructor(string memory _question) {
        question = _question;
    }

    function vote(bool chooseA) external {
        require(!hasVoted[msg.sender], "Already voted");
        hasVoted[msg.sender] = true;

        if (chooseA) {
            optionA += 1;
        } else {
            optionB += 1;
        }

        emit Voted(msg.sender, chooseA);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract FeeCollector {
    address public owner;
    uint256 public totalCollected;

    event FeeReceived(address indexed from, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    function payFee() external payable {
        require(msg.value > 0, "Must send ETH");
        totalCollected += msg.value;
        emit FeeReceived(msg.sender, msg.value);
    }

    function withdraw() external {
        require(msg.sender == owner, "Not owner");
        uint256 amount = address(this).balance;
        (bool success, ) = owner.call{value: amount}("");
        require(success, "Withdraw failed");
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Echo {
    string public lastMessage;
    address public lastSender;
    uint256 public messageCount;

    event MessageEchoed(address indexed sender, string message);

    function echo(string calldata message) external {
        lastMessage = message;
        lastSender = msg.sender;
        messageCount += 1;
        emit MessageEchoed(msg.sender, message);
    }

    function getLastMessage() external view returns (string memory, address, uint256) {
        return (lastMessage, lastSender, messageCount);
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Inventory {
    mapping(address => mapping(string => uint256)) public items;

    event ItemAdded(address indexed user, string item, uint256 amount);
    event ItemRemoved(address indexed user, string item, uint256 amount);

    function addItem(string calldata item, uint256 amount) external {
        require(amount > 0, "Amount must be > 0");
        items[msg.sender][item] += amount;
        emit ItemAdded(msg.sender, item, amount);
    }

    function removeItem(string calldata item, uint256 amount) external {
        require(items[msg.sender][item] >= amount, "Not enough items");
        items[msg.sender][item] -= amount;
        emit ItemRemoved(msg.sender, item, amount);
    }

    function getBalance(address user, string calldata item) external view returns (uint256) {
        return items[user][item];
    }
}

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract CounterMap {
    mapping(string => uint256) public counters;

    event CounterIncremented(string key, uint256 newValue);

    function increment(string calldata key) external {
        counters[key] += 1;
        emit CounterIncremented(key, counters[key]);
    }

    function get(string calldata key) external view returns (uint256) {
        return counters[key];
    }

    function reset(string calldata key) external {
        counters[key] = 0;
        emit CounterIncremented(key, 0);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MultiCounter {
    uint256 public counterA;
    uint256 public counterB;
    uint256 public counterC;

    event CounterAIncremented(uint256 newValue);
    event CounterBIncremented(uint256 newValue);
    event CounterCIncremented(uint256 newValue);

    function incrementA() external {
        counterA += 1;
        emit CounterAIncremented(counterA);
    }

    function incrementB() external {
        counterB += 1;
        emit CounterBIncremented(counterB);
    }

    function incrementC() external {
        counterC += 1;
        emit CounterCIncremented(counterC);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Flag {
    bool public flag;
    address public lastToggler;

    event FlagToggled(bool newState, address indexed by);

    function toggle() external {
        flag = !flag;
        lastToggler = msg.sender;
        emit FlagToggled(flag, msg.sender);
    }

    function setFlag(bool state) external {
        flag = state;
        lastToggler = msg.sender;
        emit FlagToggled(state, msg.sender);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleQueue {
    address[] private queue;

    event Joined(address indexed user, uint256 position);
    event Left(address indexed user);

    function join() external {
        queue.push(msg.sender);
        emit Joined(msg.sender, queue.length - 1);
    }

    function leave() external {
        require(queue.length > 0, "Queue is empty");
        address user = queue[0];
        // shift left
        for (uint256 i = 0; i < queue.length - 1; i++) {
            queue[i] = queue[i + 1];
        }
        queue.pop();
        emit Left(user);
    }

    function getQueueLength() external view returns (uint256) {
        return queue.length;
    }

    function getFirst() external view returns (address) {
        require(queue.length > 0, "Queue is empty");
        return queue[0];
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Status {
    enum State { Inactive, Active, Suspended }

    State public currentState;
    address public owner;

    event StateChanged(State newState, address indexed by);

    constructor() {
        owner = msg.sender;
        currentState = State.Inactive;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    function setActive() external onlyOwner {
        currentState = State.Active;
        emit StateChanged(State.Active, msg.sender);
    }

    function setSuspended() external onlyOwner {
        currentState = State.Suspended;
        emit StateChanged(State.Suspended, msg.sender);
    }

    function setInactive() external onlyOwner {
        currentState = State.Inactive;
        emit StateChanged(State.Inactive, msg.sender);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract IdGenerator {
    uint256 public nextId = 1;
    mapping(address => uint256) public userIds;

    event IdAssigned(address indexed user, uint256 id);

    function generateId() external returns (uint256) {
        require(userIds[msg.sender] == 0, "Already has ID");
        uint256 id = nextId;
        nextId += 1;
        userIds[msg.sender] = id;
        emit IdAssigned(msg.sender, id);
        return id;
    }

    function getId(address user) external view returns (uint256) {
        return userIds[user];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TaskManager {
    struct Task {
        string description;
        bool completed;
    }

    mapping(address => Task[]) public tasks;

    event TaskCreated(address indexed user, uint256 index, string description);
    event TaskCompleted(address indexed user, uint256 index);

    function createTask(string calldata description) external {
        tasks[msg.sender].push(Task(description, false));
        emit TaskCreated(msg.sender, tasks[msg.sender].length - 1, description);
    }

    function completeTask(uint256 index) external {
        require(index < tasks[msg.sender].length, "Invalid index");
        tasks[msg.sender][index].completed = true;
        emit TaskCompleted(msg.sender, index);
    }

    function getTaskCount(address user) external view returns (uint256) {
        return tasks[user].length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleAuth {
    mapping(address => bool) public authorized;
    address public admin;

    event Authorized(address indexed user);
    event Revoked(address indexed user);

    constructor() {
        admin = msg.sender;
        authorized[msg.sender] = true;
    }

    modifier onlyAdmin() {
        require(msg.sender == admin, "Not admin");
        _;
    }

    function authorize(address user) external onlyAdmin {
        authorized[user] = true;
        emit Authorized(user);
    }

    function revoke(address user) external onlyAdmin {
        authorized[user] = false;
        emit Revoked(user);
    }

    function isAuthorized(address user) external view returns (bool) {
        return authorized[user];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract NumberGuess {
    uint256 private secretNumber;
    address public owner;

    event Guessed(address indexed player, uint256 guess, bool correct);

    constructor(uint256 _secretNumber) {
        owner = msg.sender;
        secretNumber = _secretNumber;
    }

    function guess(uint256 number) external returns (bool) {
        bool correct = (number == secretNumber);
        emit Guessed(msg.sender, number, correct);
        return correct;
    }

    function setSecret(uint256 newSecret) external {
        require(msg.sender == owner, "Not owner");
        secretNumber = newSecret;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract AccessList {
    address public owner;
    mapping(address => bool) public allowed;

    event AccessGranted(address indexed user);
    event AccessRevoked(address indexed user);

    constructor() {
        owner = msg.sender;
        allowed[msg.sender] = true;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    function grant(address user) external onlyOwner {
        allowed[user] = true;
        emit AccessGranted(user);
    }

    function revoke(address user) external onlyOwner {
        allowed[user] = false;
        emit AccessRevoked(user);
    }

    function isAllowed(address user) external view returns (bool) {
        return allowed[user];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BidTracker {
    address public highestBidder;
    uint256 public highestBid;
    mapping(address => uint256) public bids;

    event NewBid(address indexed bidder, uint256 amount);

    function placeBid() external payable {
        require(msg.value > highestBid, "Bid too low");
        
        // devolver puja anterior si existe
        if (highestBidder != address(0)) {
            bids[highestBidder] += highestBid;
        }

        highestBidder = msg.sender;
        highestBid = msg.value;
        emit NewBid(msg.sender, msg.value);
    }

    function withdraw() external {
        uint256 amount = bids[msg.sender];
        require(amount > 0, "No funds");
        bids[msg.sender] = 0;
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success, "Transfer failed");
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract VoteTwoOptions {
    uint256 public votesFor;
    uint256 public votesAgainst;
    mapping(address => bool) public hasVoted;

    event Voted(address indexed voter, bool support);

    function vote(bool support) external {
        require(!hasVoted[msg.sender], "Already voted");
        hasVoted[msg.sender] = true;

        if (support) {
            votesFor += 1;
        } else {
            votesAgainst += 1;
        }

        emit Voted(msg.sender, support);
    }

    function getResults() external view returns (uint256 forVotes, uint256 againstVotes) {
        return (votesFor, votesAgainst);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract NameTag {
    mapping(address => string) public tags;
    mapping(string => address) public tagOwner;

    event TagClaimed(address indexed user, string tag);
    event TagReleased(address indexed user, string tag);

    function claimTag(string calldata tag) external {
        require(bytes(tag).length > 0, "Empty tag");
        require(tagOwner[tag] == address(0), "Tag already taken");
        require(bytes(tags[msg.sender]).length == 0, "Already has a tag");

        tags[msg.sender] = tag;
        tagOwner[tag] = msg.sender;
        emit TagClaimed(msg.sender, tag);
    }

    function releaseTag() external {
        string memory tag = tags[msg.sender];
        require(bytes(tag).length > 0, "No tag");
        delete tagOwner[tag];
        delete tags[msg.sender];
        emit TagReleased(msg.sender, tag);
    }
}
