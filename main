import pymorphy2
import spacy

nlp = spacy.load('ru_core_news_sm')
morph = pymorphy2.MorphAnalyzer()


cases = {'nom': 'nomn', 'gen': 'gent', 'dat': 'datv', 'acc': 'accs', 'abl': 'ablt', 'loc': 'loct'}
numbers = ('sing', 'plur')


def replace_noun(word, number, case):
    word = morph.parse(word)[0]
    case = cases[case]
    return f"{word.inflect({number, case}).word}".capitalize()


def put_name_in_template(path):
    fin = open("data/test.txt", encoding="utf8")
    fout = open("data/out_test.txt", 'w', encoding="utf-8")
    lines = fin.readlines()
    print("Введите имя главного героя: ")
    main_character = input()
    for line in lines:
        words = line.split()
        for word in words:
            if "{" in word:
                k = words.index(word)
                word = word[1:-1:]
                patterns = word.split("|")
                word = replace_noun(main_character, patterns[0], patterns[1])
                words[k] = word
        print(" ".join(words), file=fout)
    fin.close()
    fout.close()


def insert_into_template(words_to_insert_path, template_path):
    charachters = {}
    file_to_insert = open(words_to_insert_path, encoding="utf8")
    template = open(template_path, encoding="utf8")
    fout = open("data/template_out.txt", 'w', encoding="utf-8")

    lines_to_insert = file_to_insert.readlines()
    for line in lines_to_insert:
        names = line.split()
        charachter_type = names[0]
        charachters[charachter_type] = names[1::]

    lines = template.readlines()
    for line in lines:
        words = line.split()
        for word in words:
            if "|" in word:
                k = words.index(word)
                patterns = word.split("|")
                charachter_type = patterns[1]
                charachter_name = charachters[charachter_type][int(patterns[2])]
                word = replace_noun(charachter_name, patterns[3], patterns[4])
                words[k] = word
        print(" ".join(words), file=fout, sep="")
    file_to_insert.close()
    template.close()
    fout.close()


def change_tale_to_template(path):
    charachters = {}
    fin = open(path, encoding="utf8")
    fout = open("data/data_out.txt", 'w', encoding="utf-8")
    line = fin.readline()
    while line != "start\n":
        names = line.split()
        charachter_type = names[0]
        charachters[charachter_type] = names[1::]
        line = fin.readline()
    lines = fin.readlines()
    for line in lines:
        doc = nlp(line)
        for token in doc:
            lemma = token.lemma_
            for charachter in charachters.items():
                names = charachter[1]
                charachter_type = charachter[0]
                if lemma in names:
                    name_index = names.index(lemma)
                    pattern = [token.morph.get("Number")[0], token.morph.get("Case")[0]]
                    template = "|" + "|".join([charachter_type, str(name_index), pattern[0].lower(), pattern[1].lower()]) + "|"
                    line = line.replace(token.lemma_, template)
                    break
        print(line, file=fout)
    fin.close()
    fout.close()


def main():
    change_tale_to_template("data/data.txt")
    insert_into_template("data/to_insert_template.txt", "data/template.txt")


main()
