# Миргасимов и Фролов 
### 15.27В-ПИ12/24м 
### ТВОРЧЕСКИЙ РЕЙТИНГ НС-3
# [Ссылка на Google Colab](https://colab.research.google.com/drive/1HmZOucQqlpSuVmqQRNPA2PGlQg4vK_lE)
# [Код с комментариями](https://github.com/mirgasimov/HC-3_TR/blob/main/%D0%BC%D0%B8%D1%80%D0%B3%D0%B0%D1%81%D0%B8%D0%BC%D0%BE%D0%B2_%D1%84%D1%80%D0%BE%D0%BB%D0%BE%D0%B2(15_27%D0%B2_%D0%BF%D0%B812_24%D0%BC)_%D1%82%D0%B2%D0%BE%D1%80%D1%87%D0%B5%D1%81%D0%BA%D0%B8%D0%B9_%D1%80%D0%B5%D0%B9%D1%82%D0%B8%D0%BD%D0%B3.py)
# [Данных MNIST](https://storage.googleapis.com/tensorflow/tf-keras-datasets/mnist.npz)

### Было
> Точность на проверочных данных:  0.9786999821662903
> ![image](https://github.com/user-attachments/assets/89f80b66-7ee9-4274-886b-1ba7c8e491b8)
### Стало
> Точность на проверочных данных: 0.9824000000953674
![image](https://github.com/user-attachments/assets/2f1a2abc-48bd-471f-a135-a4194f9cf1d8)


### Было: 0.9787
![image](https://github.com/user-attachments/assets/a2003a7b-d24d-493a-a02f-49a52503f7d2)


### Стало: 0.9824
![image](https://github.com/user-attachments/assets/65c11db5-291d-4b63-94ce-b29a4591581d)


> Итог: точность улучшилась примерно на 0.38%.
> Улучшение небольшое, но положительное.



Оптимизация кода была проведена за счёт нескольких изменений:  

### 1. **Использование сверточных слоёв (Conv2D) вместо Dense**
#### **До**:
- Входные изображения превращались в одномерный вектор (Flatten) и сразу передавались в полносвязные (Dense) слои.
- Это не учитывало пространственную структуру изображений, что снижало эффективность.

#### **После**:
- Добавлены сверточные слои (Conv2D), которые извлекают пространственные признаки изображения.
- Макспулинг (MaxPooling2D) уменьшает размерность, выделяя важные части изображения.
---

### 2. **Добавлена аугментация данных**
#### **До**:
- Использовался исходный набор без изменений.

#### **После**:
```python
datagen = ImageDataGenerator(
    rotation_range=10,
    width_shift_range=0.1,
    height_shift_range=0.1,
    zoom_range=0.1
)
datagen.fit(train_images)
``` 
- Вращение ```rotation_range```, смещение ```width_shift_range```, ```height_shift_range```), зум ```zoom_range``` искусственно увеличивают количество тренировочных данных.

Это снижает переобучение и улучшает обобщающую способность модели.

---

### 3. **Добавлены слои Dropout и L2-регуляризация**
#### **До**:
- Полносвязные слои без регулизации, что могло привести к переобучению.

#### **После**:
```python
Dense(256, activation='relu', kernel_regularizer=keras.regularizers.l2(0.01)),
Dropout(0.3),
Dense(128, activation='relu', kernel_regularizer=keras.regularizers.l2(0.01)),
Dropout(0.3),
``` 
- Dropout отключает случайные нейроны во время обучения, снижая переобучение.
- L2-регуляризация добавляет штраф за слишком большие веса.

👉 Это улучшает обобщающую способность модели.

---


### 4. **Изменение процесса обучения**
#### **До**:
```python
history = model.fit(train_images, train_labels, epochs=15, batch_size=128, validation_data=(validation_images, validation_labels))
``` 
- Использовался обычный fit без аугментации.

#### **После**:
```python
history = model.fit(
    datagen.flow(train_images, train_labels, batch_size=128),
    epochs=20,
    validation_data=(validation_images, validation_labels)
)
```
- Вместо передачи данных напрямую теперь используется генератор datagen.flow().


---

## **Вывод**
**Использования сверточных слоёв (Conv2D + MaxPooling2D)** → лучше распознаёт образы.  
**Добавления аугментации (ImageDataGenerator)** → снижает переобучение.  
