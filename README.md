# Hi there, I'm Olaf 👋

## Full-Stack & Mobile Developer | Multi-Stack Specialist

I build web and mobile applications across diverse technology stacks, specializing in creating scalable, maintainable solutions with excellent user experiences.

### 🛠️ My Tech Stack
- **Mobile**: Flutter
- **Frontend**: React, Vue.js, Angular, TypeScript, JavaScript, HTML/CSS
- **Backend**: Node.js, Express, Laravel, Django, Ruby on Rails.
- **Databases**: MongoDB, MySQL, PostgreSQL
- **DevOps**: Git, Docker, AWS
- **Tools**: Postman, VS Code, Figma, Jira

### Contact
📧 Email: stardevelop93@gmail.com

### 💻 Code Sample Showcase
```javascript
// Node.js API middleware example
const validateRequest = async (req, res, next) => {
  try {
    const schema = getValidationSchema(req.path);
    await schema.validateAsync(req.body);
    next();
  } catch (error) {
    return res.status(400).json({
      status: 'error',
      message: error.details[0].message
    });
  }
};


