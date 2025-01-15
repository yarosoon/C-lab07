# C#-lab07 
## Цели работы:
Научиться работать с механизмом рефлексии языка C#.

## Задание №1

Создайте проект библиотеки классов со следующей диаграммой классов

![image](https://github.com/user-attachments/assets/90272c15-5bce-44c5-955e-71e839e784cb)

В созданном проекте реализуйте класс пользовательского атрибута со свойством Comment. Примените атрибут с произвольным комментарием к каждому классу диаграммы классов.
Создайте приложение, которое будет подключать созданную библиотеку классов и средствами рефлексии генерировать файл xml-представления всей диаграммы классов библиотеки, в том числе с созданными пользовательскими атрибутами.

## Код задания №1 (ClassLibrary)

    namespace lab07_cl
    {
        // Указывает, что этот атрибут может быть применен только к классам.
        [AttributeUsage(AttributeTargets.Class)]
        public class CommentAtt : Attribute // Определяет пользовательский атрибут для комментариев к классам.
        {
            // Свойство для хранения комментария к классу.
            public string Comment { get; set; }
    
            // Конструктор для инициализации комментария.
            public CommentAtt(string Comment)
            {
                this.Comment = Comment;
            }
        }
    
        // Перечисление для классификации животных по типу питания.
        public enum eClassificationAnimal
        {
            Herbivores, // Травоядные
            Carnivores, // Плотоядные
            Omnivores   // Всеядные
        }
    
        // Перечисление для предпочтений животных в еде.
        public enum eFavouriteFood
        {
            Meat,       // Мясо
            Plants,     // Растения
            Everything  // Всё подряд
        }
    
        // Применяем атрибут к абстрактному классу для описания животных.
        [CommentAtt("Абстрактный класс для объектов, представляющих животных")]
        public abstract class Animal
        {
            // Свойство для страны обитания животного.
            public string Country { get; set; }
    
            // Свойство, определяющее, скрывается ли животное от других.
            public bool HideFromOtherAnimals { get; set; }
    
            // Имя животного.
            public string Name { get; set; }
    
            // Вид животного (например, «лев» или «свинья»).
            public string WhatAnimal { get; set; }
    
            // Конструктор для инициализации свойств животного.
            public Animal(string country, bool hideFromAnimals, string name, string whatAnimal)
            {
                (Country, HideFromOtherAnimals, Name, WhatAnimal) = (country, hideFromAnimals, name, whatAnimal);
            }
    
            // Абстрактный метод для получения классификации животного.
            public abstract eClassificationAnimal GetClassificationAnimal();
    
            // Абстрактный метод для получения предпочтений в еде.
            public abstract eFavouriteFood GetFavouriteFood();
    
            // Абстрактный метод, который возвращает звук животного.
            public abstract string SayHello();
    
            // Метод для "распаковки" данных животного.
            public void Deconstruct(out string country, out bool hideFromOtherAnimals, out string name, out string whatAnimal)
            {
                country = Country;
                hideFromOtherAnimals = HideFromOtherAnimals;
                name = Name;
                whatAnimal = WhatAnimal;
            }
        }
    
        // Применяем атрибут к классу для описания коровы.
        [CommentAtt("Класс описания коровы")]
        public class Cow : Animal
        {
            // Конструктор для инициализации свойств коровы.
            public Cow(string country, bool hideFromOtherAnimals, string name, string whatAnimal) :
                base(country, hideFromOtherAnimals, name, whatAnimal)
            { }
    
            // Возвращает предпочтения коровы в еде.
            public override eFavouriteFood GetFavouriteFood()
            {
                return eFavouriteFood.Plants;
            }
    
            // Возвращает классификацию коровы как травоядного.
            public override eClassificationAnimal GetClassificationAnimal()
            {
                return eClassificationAnimal.Herbivores;
            }
    
            // Возвращает звук, издаваемый коровой.
            public override string SayHello()
            {
                return "Moo";
            }
        }
    
        // Применяем атрибут к классу для описания льва.
        [CommentAtt("Класс описания льва")]
        public class Lion : Animal
        {
            // Конструктор для инициализации свойств льва.
            public Lion(string country, bool hideFromOtherAnimals, string name, string whatAnimal) :
                base(country, hideFromOtherAnimals, name, whatAnimal)
            { }
    
            // Возвращает предпочтения льва в еде.
            public override eFavouriteFood GetFavouriteFood()
            {
                return eFavouriteFood.Meat;
            }
    
            // Возвращает классификацию льва как плотоядного.
            public override eClassificationAnimal GetClassificationAnimal()
            {
                return eClassificationAnimal.Omnivores;
            }
    
            // Возвращает звук, издаваемый львом.
            public override string SayHello()
            {
                return "Meow";
            }
        }
    
        // Применяем атрибут к классу для описания свиньи.
        [CommentAtt("Класс описания свиньи")]
        public class Pig : Animal
        {
            // Конструктор для инициализации свойств свиньи.
            public Pig(string country, bool hideFromOtherAnimals, string name, string whatAnimal) :
                base(country, hideFromOtherAnimals, name, whatAnimal)
            { }
    
            // Возвращает предпочтения свиньи в еде.
            public override eFavouriteFood GetFavouriteFood()
            {
                return eFavouriteFood.Everything;
            }
    
            // Возвращает классификацию свиньи как всеядного.
            public override eClassificationAnimal GetClassificationAnimal()
            {
                return eClassificationAnimal.Omnivores;
            }
    
            // Возвращает звук, издаваемый свиньей.
            public override string SayHello()
            {
                return "Oink";
            }
        }
    }
    
## Код задания №1 (ConsoleApp)    

    using lab07_cl; // Подключаем пространство имен библиотеки классов, чтобы использовать ее типы.
    using System.Reflection; // Подключаем пространство имен для работы с рефлексией.
    using System.Reflection.Metadata; // Подключаем пространство имен для метаданных сборки.
    using System.Xml.Linq; // Подключаем пространство имен для работы с XML-документами.
    
    namespace ReflectionApp // Определяем новое пространство имен для программы рефлексии.
    {
        class Program // Определяем класс Program, содержащий точку входа в приложение.
        {
            static void Main(string[] args) // Определяем главный метод Main, который запускается при старте приложения.
            {
                // Укажем путь к сборке библиотеки классов
                string assemblyPath = "lab07_CL.dll"; // Указываем путь к сборке, которую будем анализировать.
                Assembly assembly = Assembly.LoadFrom(assemblyPath); // Загружаем сборку с указанного пути.
    
                // Создадим корневой xml элемент
                XElement xmlRoot = new XElement("AnimalLib"); // Создаем корневой XML-элемент для документа.
    
                // Перебираем все классы в сборке с помощью рефлексии
                foreach (Type type in assembly.GetTypes()) // Для каждого типа (класса, интерфейса и т. д.) в сборке:
                {
                    XElement classElement = new XElement("Class", new XAttribute("Name", type.Name)); // Создаем элемент XML для класса и добавляем его имя как атрибут.
    
                    // Пользовательские атрибуты класса, если они есть
                    var classAttributes = type.GetCustomAttributes(typeof(CommentAtt), false); // Получаем все атрибуты класса типа CommentAtt.
                    foreach (CommentAtt attr in classAttributes) // Для каждого найденного атрибута:
                    {
                        classElement.Add(new XElement("Comment", attr.Comment)); // Добавляем комментарий как XML-элемент.
                    }
    
                    // Перебираем все свойства класса
                    foreach (PropertyInfo property in type.GetProperties()) // Для каждого свойства класса:
                    {
                        XElement propertyElement = new XElement("Property",
                            new XAttribute("Name", property.Name), // Добавляем имя свойства как атрибут.
                            new XAttribute("Type", property.PropertyType.Name)); // Добавляем тип свойства как атрибут.
                        classElement.Add(propertyElement); // Добавляем элемент свойства в элемент класса.
                    }
    
                    // Все методы класса
                    foreach (MethodInfo method in type.GetMethods()) // Для каждого метода класса:
                    {
                        XElement methodElement = new XElement("Method",
                            new XAttribute("Name", method.Name), // Добавляем имя метода как атрибут.
                            new XAttribute("ReturnType", method.ReturnType.Name)); // Добавляем тип возвращаемого значения как атрибут.
    
                        // Если метод принимает параметры, добавляем информацию о каждом параметре
                        foreach (ParameterInfo param in method.GetParameters()) // Для каждого параметра метода:
                        {
                            XElement paramElement = new XElement("Parameter",
                                new XAttribute("Name", param.Name), // Добавляем имя параметра как атрибут.
                                new XAttribute("Type", param.ParameterType.Name)); // Добавляем тип параметра как атрибут.
                            methodElement.Add(paramElement); // Добавляем элемент параметра в элемент метода.
                        }
                        classElement.Add(methodElement); // Добавляем элемент метода в элемент класса.
                    }
    
                    // Проверяем, является ли тип перечислением (enum)
                    if (type.IsEnum) // Если тип является перечислением:
                    {
                        foreach (var enumValue in Enum.GetValues(type)) // Перебираем все значения перечисления:
                        {
                            XElement enumElement = new XElement("EnumValue",
                                new XAttribute("Name", enumValue.ToString()), // Добавляем имя значения перечисления.
                                new XAttribute("Value", ((int)enumValue).ToString())); // Добавляем числовое значение перечисления.
                            classElement.Add(enumElement); // Добавляем элемент значения перечисления в элемент класса.
                        }
                    }
    
                    xmlRoot.Add(classElement); // Добавляем элемент класса в корневой элемент XML.
                }
    
                // Создаем XML-документ с объявлением версии и кодировки
                XDocument xmlDocument = new XDocument(new XDeclaration("1.0", "utf-8", "yes"), xmlRoot); // Создаем XML-документ с корневым элементом.
                xmlDocument.Save("LibStructure.xml"); // Сохраняем XML-документ в файл.
    
                Console.WriteLine("File generated successfully"); // Выводим сообщение об успешном завершении работы программы.
            }
        }
    }

C# lab07
