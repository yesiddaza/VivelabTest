function Item(name, sell_in, quality) {
  this.name = name;
  this.sell_in = sell_in;
  this.quality = quality;
}

var items = [];

const sulfurasName = 'Sulfuras, Hand of Ragnaros';
const backstageName = 'Backstage passes to a TAFKAL80ETC concert';
const conjuredName = 'Conjured Mana Cake';
const agedBrieName = 'Aged Brie';

items.push(new Item('+5 Dexterity Vest', 10, 20));
items.push(new Item(agedBrieName, 2, 0));
items.push(new Item('Elixir of the Mongoose', 5, 7));
items.push(new Item(sulfurasName, 0, 80));
items.push(new Item(backstageName, 15, 20));
items.push(new Item(conjuredName, 3, 6));

function updateQuality() {
  for (var i = 0; i < items.length; i++) {
    var itemName = items[i].name;
    var itemQuality = getQualityValueByItemName(itemName, items[i].quality, items[i].sell_in);
    items[i].sell_in = getSellInValue(itemName, items[i].sell_in);
    items[i].quality = (items[i].sell_in < 0)? getQualityValue(itemName, itemQuality) : itemQuality;
  }
}

function isAgedBrieItem(itemName) {
    return itemName == agedBrieName;
}

function isSulfuraItem(itemName) {
    return itemName == sulfurasName;
}

function isBackstageItem(itemName) {
    return itemName == backstageName;
}

function isConjuredItem(itemName) {
    return itemName == conjuredName;
}

function getQualityValueByItemName(itemName, qualityValue, sellInValue) {
    if (isAgedBrieItem(itemName) || isBackstageItem(itemName)) {
        qualityValue = addOneValueToQuality(qualityValue);
        qualityValue = (isBackstageItem(itemName))? getBackstageQuality(sellInValue, qualityValue) : qualityValue;
    }
    else {
        qualityValue = (qualityValue > 0)? subtractValueToQuality(itemName, qualityValue) : qualityValue;
    }
    return qualityValue;
}

function getQualityValue(itemName, qualityValue) {
    return (isAgedBrieItem(itemName))? addOneValueToQuality(qualityValue) : getQualityExceptToAgedBrieItem(itemName, qualityValue);
}

function getQualityExceptToAgedBrieItem(itemName, qualityValue) {
    return (isBackstageItem(itemName))? 0 : getQualityWhenDaysHasOver(itemName, qualityValue);
}

function getQualityWhenDaysHasOver(itemName, qualityValue) {
    return (qualityValue > 0)? subtractValueToQuality(itemName, qualityValue) : qualityValue;
}

function getBackstageQuality(sellInValue, qualityValue) {
    qualityValue = getValueToBackstageQualityByLimitDays(sellInValue, 11, qualityValue);
    qualityValue = getValueToBackstageQualityByLimitDays(sellInValue, 6, qualityValue);
    return qualityValue;
}

function getSellInValue(itemName, sellInValue) {
    return (isSulfuraItem(itemName))? 0 : sellInValue - 1;
}

function getValueToBackstageQualityByLimitDays(sellInValue, sellInLimitDays, qualityValue) {
    return (sellInValue < sellInLimitDays)? addOneValueToQuality(qualityValue) : qualityValue;
}

function addOneValueToQuality(qualityValue) {
    return (qualityValue < 50)? qualityValue + 1 : qualityValue
}

function subtractValueToQuality(itemName, qualityValue) {
    return (isSulfuraItem(itemName))? qualityValue : (isConjuredItem(itemName))? qualityValue - 2 : qualityValue - 1;
}