카드 게임 개발

```dart
void main() {
  var deck = new Deck();
  print(deck);
}

class Deck {
  List<Card> cards = [];
  
  Deck() {    
    var ranks = ['Ace', 'Two', 'Three', 'Four', 'Five'];
    var suits = ['Diamonds', 'Hearts', 'Clubs', 'Spades'];
    
    for(var suit in suits) {      
      for (var rank in ranks) {
        var card = new Card(suit, rank);        
        cards.add(card);
      }
    }    
  }
  
  @override 
  String toString() {
    return cards.join(",");
  }
  
  
}

class Card {
  String suit;
  String rank;
  
  Card(this.suit, this.rank);
  
  @override
  String toString() {
    return '{$suit,$rank}';
  }
  
}
```





