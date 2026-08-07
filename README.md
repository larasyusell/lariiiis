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
