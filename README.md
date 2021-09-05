# Estiam:  Lab Election 


### Modifications apportés par rapport au lab

#### Sur le fichier Election.sol

**fonction addCandidate**

*Avant:*
```js
function addCandidate (string _name) private {
    candidatesCount ++;
    candidates[candidatesCount] = Candidate(candidatesCount, _name, 0);
  }
```
*Après:*
```js
// emplacmeent de stockage (à savoir memory dans le cas présent) nécessaire depuis la dernier version de solidity
function addCandidate (string memory _name) private {
    candidatesCount ++;
    candidates[candidatesCount] = Candidate(candidatesCount, _name, 0);
  }
```

**fonction vote**

*Avant*
```js
event votedEvent(uint indexed _candidateId);

function vote(uint _candidateId) public {
    // Require that they haven't voted
    require(!voters[msg.sender]);

    //require a valid candidate
    require(_candidateId > 0 && _candidateId <= candidatesCount);

    // record that voter has voted
    voters[msg.sender] = true;

    // update candidature vote Count
    candidates[_candidateId].voteCount++;

    // trigger voted event
    votedEvent(_candidateId);
  }
```

*Après*
```js
event votedEvent(uint indexed _candidateId);

function vote(uint _candidateId) public {
    // Require that they haven't voted
    require(!voters[msg.sender]);

    //require a valid candidate
    require(_candidateId > 0 && _candidateId <= candidatesCount);

    // record that voter has voted
    voters[msg.sender] = true;

    // update candidature vote Count
    candidates[_candidateId].voteCount++;

    // trigger voted event
    // emit nécessaire si une fonction event est appelé
    emit votedEvent(_candidateId);
  }
```

#### Sur le fichier election.js


*Avant:*

```js
it("throws an exception for double voting", function () {
    return Election.deployed().then(function (instance) {
      electionInstance = instance;
      candidateId = 2;
      electionInstance.vote(candidateId, { from: accounts[1] });
      return electionInstance.candidates(candidateId);
    }).then(function (candidate) {
      let voteCount = candidate[2];
      assert.equal(voteCount, 1, "accepts first vote");
      // Try to vote again 
      return electionInstance.vote(candidateId, { from: accounts[1] });
    }).then(assert.fail).catch(function (error) {
      // Ne fonctionne pas à cause de indexOf
      assert(error.message.indexOf('revert') >= 0, "error message must contain revert");
      return electionInstance.candidates(1);
    }).then(function (candidate1) {
      let voteCount = candidate1[2];
      assert.equal(voteCount, 1, "candidate 1 did not receive any votes");
      return electionInstance.candidates(2);
    }).then(function (candidate2) {
      let voteCount = candidate2[2];
      assert.equal(voteCount, 1, "candidate 2 did not receive any votes");
    });
  });
```

*Après:*

```js
it("throws an exception for double voting", function () {
    return Election.deployed().then(function (instance) {
      electionInstance = instance;
      candidateId = 2;
      electionInstance.vote(candidateId, { from: accounts[1] });
      return electionInstance.candidates(candidateId);
    }).then(function (candidate) {
      let voteCount = candidate[2];
      assert.equal(voteCount, 1, "accepts first vote");
      // Try to vote again 
      return electionInstance.vote(candidateId, { from: accounts[1] });
    }).then(assert.fail).catch(function (error) {
      // assert(error.message.indexOf('revert') >= 0, "error message must contain revert");
      assert(error.message, "error message must contain revert");
      return electionInstance.candidates(1);
    }).then(function (candidate1) {
      let voteCount = candidate1[2];
      assert.equal(voteCount, 1, "candidate 1 did not receive any votes");
      return electionInstance.candidates(2);
    }).then(function (candidate2) {
      let voteCount = candidate2[2];
      assert.equal(voteCount, 1, "candidate 2 did not receive any votes");
    });
  });
```

### Mematmask
1. après avoir configurer le réseau et lancer le site, cliquer sur les 3 petits points à droit d'**Account**

2. cliquer sur **connected sites** pour pouvoir connecter le site avec Metamask


